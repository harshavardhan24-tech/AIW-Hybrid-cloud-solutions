# Exercise 1: Preparing environment with the prerequisites to deploy Azure Local (READ-ONLY)

### Estimated Duration: 30 Minutes

### 📌Please note that this exercise has already been performed in the lab environment, but please go through the steps to get familiar.

## Overview

In this exercise, you'll be preparing the environment for deploying Azure Local, which involves installing and configuring the necessary operating system (e.g., Windows Server), along with any required drivers and software. Additionally, configuring networking components such as switches and routers to meet Azure Local's networking requirements is essential for successful deployment.

## Objectives

You will be able to complete the following tasks:

- Task 1: Review the configured virtualized Azure Local VMs
- Task 2: Onboard Azure Arc Machine to Azure and prepare to deploy Azure Local

## Task 1: Review the configured virtualized Azure Local VMs 

In this task, you will use Hyper-V Manager on the Localbox-Client VM to review the status of the deployed Azure Local virtual machines.

1. In the Localbox-Client VM, search for **Hyper-V Manager (1)** in the search box. Select **Hyper-V Manager (2)**.

   ![](./media/hci24-1a.png)
    
2. From Hyper-V Manager, click on **LOCALBOX-CLIENT (1)** and review that the **AzLHOST1**, **AzLHOST2**, and **AzLMGMT** virtual machines **(2)** are up and running, as shown in the screenshot below.

   ![](./media/ex1.png)

## Task 2: Onboard Azure Arc Machine to Azure and prepare to deploy Azure Local 

In this task, you will review the process of onboarding Azure Local hosts to Azure Arc using PowerShell and verify the registered Arc machines.

1. In the Windows search bar, type **PowerShell ISE** **(1)**, and select Windows **PowerShell ISE** **(2)** to open it from the Lab VM.

   ![](./media/hci24-3a.png)

2. Run the commands below to onboard Azure Arc Machines to Azure:

   >**Note**:  Please note that this lab has already been performed in the lab environment; however, please go through the steps to get familiar. You do not need to run the script below.

    ```
          function Set-AzLocalDeployPrereqs {
   param (
       $LocalBoxConfig,
       [PSCredential]$localCred,
       [PSCredential]$domainCred
   )
   Invoke-Command -VMName $LocalBoxConfig.MgmtHostConfig.Hostname -Credential $localCred -ScriptBlock {
       $LocalBoxConfig = $using:LocalBoxConfig
       $localCred = $using:localcred
       $domainCred = $using:domainCred
       Invoke-Command -VMName $LocalBoxConfig.DCName -Credential $domainCred -ArgumentList $LocalBoxConfig -ScriptBlock {
           $LocalBoxConfig = $args[0]
           $domainCredNoDomain = new-object -typename System.Management.Automation.PSCredential `
               -argumentlist ($LocalBoxConfig.LCMDeployUsername), (ConvertTo-SecureString $LocalBoxConfig.SDNAdminPassword -AsPlainText -Force)

           Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force -Scope CurrentUser
           Install-Module AsHciADArtifactsPreCreationTool -Repository PSGallery -Force -Confirm:$false
           $domainName = $LocalBoxConfig.SDNDomainFQDN.Split('.')
           $ouName = "OU=$($LocalBoxConfig.LCMADOUName)"
           foreach ($name in $domainName) {
               $ouName += ",DC=$name"
           }
           $nodes = @()
           foreach ($node in $LocalBoxConfig.NodeHostConfig) {
               $nodes += $node.Hostname.ToString()
           }
           Add-KdsRootKey -EffectiveTime ((Get-Date).AddHours(-10))
           New-HciAdObjectsPreCreation -AzureStackLCMUserCredential $domainCredNoDomain -AsHciOUName $ouName
       }
   }

   foreach ($node in $LocalBoxConfig.NodeHostConfig) {
       Invoke-Command -VMName $node.Hostname -Credential $localCred -ArgumentList $env:subscriptionId, $env:spnTenantId, $env:spnClientID, $env:spnClientSecret, $env:resourceGroup, $env:azureLocation -ScriptBlock {
           $subId = $args[0]
           $tenantId = $args[1]
           $clientId = $args[2]
           $clientSecret = $args[3]
           $resourceGroup = $args[4]
           $location = $args[5]

           function ConvertFrom-SecureStringToPlainText {
               param (
                   [Parameter(Mandatory = $true)]
                   [System.Security.SecureString]$SecureString
               )

               $Ptr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($SecureString)
               try {
                   return [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($Ptr)
               }
               finally {
                   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($Ptr)
               }
           } 

           Connect-AzAccount -Identity -Tenant $Env:tenantId -Subscription $Env:subscriptionId
           $armtoken = ConvertFrom-SecureStringToPlainText -SecureString ((Get-AzAccessToken -AsSecureString).Token)

           #Invoke the registration script.
           Invoke-AzStackHciArcInitialization -SubscriptionID $subId -ResourceGroup $resourceGroup -TenantID $tenantId -Region $location -Cloud "AzureCloud" -ArmAccessToken $armtoken -AccountID $clientId -ErrorAction Continue
   
       }
   }
   }
   
             
   function Wait-AzureEdgeBootstrap {
       param (
           [string]$VMName,
           [PSCredential]$Credential,
           [int]$TimeoutSeconds = 300,
           [int]$RetryIntervalSeconds = 10
       )

    $pathsToCheck = @(
        'C:\Windows\System32\WindowsPowerShell\v1.0\Modules\AzureEdgeBootstrap',
        'C:\Program Files\WindowsPowerShell\Modules\Az.Accounts'
    )

    $scriptBlock = {
        param($paths, $timeout, $interval)

        $elapsed = 0

        while ($true) {
            $status = foreach ($path in $paths) {
                $exists = Test-Path $path
                [PSCustomObject]@{
                    Path   = $path
                    Exists = $exists
                }
            }

            $missing = $status | Where-Object { -not $_.Exists }

            Write-Host "🔍 Path check status at $((Get-Date).ToString("HH:mm:ss")):"
            foreach ($entry in $status) {
                if ($entry.Exists) {
                    Write-Host "✅ $($entry.Path)"
                } else {
                    Write-Host "❌ $($entry.Path)"
                }
            }

            if (-not $missing) {
                Write-Host "✅ All required paths found. Continuing."
                break
            }

            Start-Sleep -Seconds $interval
            $elapsed += $interval

            if ($elapsed -ge $timeout) {
                $missingPaths = $missing.Path -join ', '
                throw "❌ Timeout waiting for required paths: $missingPaths"
            }
        }
    }

    Invoke-Command -VMName $VMName -Credential $Credential -ScriptBlock $scriptBlock -ArgumentList $pathsToCheck, $TimeoutSeconds, $RetryIntervalSeconds
      }
      
      function Invoke-AzureEdgeBootstrap {
          param (
              $LocalBoxConfig,
              [PSCredential]$localCred
          )

    foreach ($node in $LocalBoxConfig.NodeHostConfig) {
        Write-Host "🛠️ Running bootstrap script on $($node.Hostname)..."
        Invoke-Command -VMName $node.Hostname -Credential $localCred -ScriptBlock {
          # & C:\startupScriptsWrapper.ps1 'C:\BootstrapPackage\bootstrap\content\Bootstrap-Setup.ps1 -Install'
          Get-ScheduledTask -TaskName ImageCustomizationScheduledTask | Start-ScheduledTask
        }

        Write-Host "⏳ Waiting for modules and agent on $($node.Hostname)..."
        Wait-AzureEdgeBootstrap -VMName $node.Hostname -Credential $localCred
    }
      }
      
      
      $LocalBoxConfig = Import-PowerShellDataFile -Path $Env:LocalBoxConfigFile
      
      $localCred = new-object -typename System.Management.Automation.PSCredential `
          -argumentlist "Administrator", (ConvertTo-SecureString $LocalBoxConfig.SDNAdminPassword -AsPlainText -Force)
      
      $domainCred = new-object -typename System.Management.Automation.PSCredential `
          -argumentlist (($LocalBoxConfig.SDNDomainFQDN.Split(".")[0]) +"\Administrator"), (ConvertTo-SecureString $LocalBoxConfig.SDNAdminPassword -AsPlainText -Force)
      
      
      $LocalBoxConfig = Import-PowerShellDataFile -Path $Env:LocalBoxConfigFile
      # Login to Azure to get access on the nodes
      $azureAppCred = (New-Object System.Management.Automation.PSCredential $env:spnClientID, (ConvertTo-SecureString -String $env:spnClientSecret -AsPlainText -Force))
      Connect-AzAccount -ServicePrincipal -SubscriptionId $env:subscriptionId -TenantId $env:spnTenantId -Credential $azureAppCred
      
      $tags = Get-AzResourceGroup -Name $env:resourceGroup | Select-Object -ExpandProperty Tags
      
      if ($null -ne $tags) {
         $tags['DeploymentProgress'] = $DeploymentProgressString
      } else {
         $tags = @{'DeploymentProgress' = $DeploymentProgressString }
      }
      
      $null = Set-AzResourceGroup -ResourceGroupName $env:resourceGroup -Tag $tags
      $null = Set-AzResource -ResourceName $env:computername -ResourceGroupName $env:resourceGroup -ResourceType 'microsoft.compute/virtualmachines' -Tag $tags -Force
      
      Invoke-AzureEdgeBootstrap -LocalBoxConfig $LocalBoxConfig -localCred $localCred
      
      Set-AzLocalDeployPrereqs -LocalBoxConfig $LocalBoxConfig -localCred $localCred -domainCred $domainCred
    ```

3. Navigate to the Azure portal and verify the Azure Arc Machines onboarded to Azure, named **AzLHOST1** and **AzLHOST2**.

    >**Note**: If you see that only one Azure Arc machine got onboarded, please re-perform the previous step to complete the onboarding. 

   ![](./media/ex2.png)

## Summary

In this exercise, review the configured virtualized Azure Local VMs and onboard the Azure Arc Machine to Azure and prepare to deploy Azure Local.

### You have successfully completed the lab. Click on Next >> to proceed with the next exercise.

![](./media/pg-02.jpg)
