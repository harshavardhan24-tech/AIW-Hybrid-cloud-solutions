# Exercise 2: Deploying Azure Local in Azure Portal (READ-ONLY)

### Estimated Duration: 30 Minutes

### Please note that this exercise has already been performed in the lab environment, but please go through the steps to get familiar.

## Overview

In this exercise, you will be deploying an Azure Local solution using a generated ARM template. You can deploy it either through the Azure portal by uploading the template and specifying deployment parameters or using PowerShell for automated deployment, offering flexibility and control over the deployment process. Here, you will be deploying Azure Local using PowerShell commands.

## Objectives

You will be able to complete the following tasks:

- Task 1: Create and review the generated ARM template
- Task 2: Validate and deploy the Azure Local using PowerShell

## Task 1: Review the already generated ARM template

In this task, you will review the pre-generated ARM template and parameter files for Azure Local in VS Code and assign the Azure Arc permission to the Azure Local resource provider.

1. In the Localbox-Client VM searchbar, type **VS Code (1)**, then click on **Visual Studio Code (2)** from the results.

    ![](./media/Ex2-11.png)

1. Click on **File (1)** from the top left corner, from the list select **Open Folder... (2)**.

    ![](./media/Ex2-9.png)

1. On the Open Folder window, navigate to **C:\ (1)** path, select **LocalBox (2)** folder, then click on **Select Folder (3)**.

    ![](./media/Ex2-10.png)

1. If the **Do you trust the authors of the files in this folder?** option is prompted, click on **Yes, I trust the authors**.

1.  Navigate to the **Azure portal**, Click on the **CloudShell** from the top and then select the **Powershell**.

    ![](./media/lab02a1.png)

1. Click on **No storage account required (1)**, choose your subscription **(2)**, and then click **Apply (3)**.

    >**Note:** Ensure that you select the subscription whose name begins with **Azure HOL - xxxxxx**

    ![](./media/lab02a2.png)

1. Once PowerShell is ready, run the commands below to register the required Resource Providers.

   >**Note:** It will take 2-3 minutes to get registered.

    ```
    Register-AzResourceProvider -ProviderNamespace "Microsoft.HybridCloud"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.HybridCompute"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.GuestConfiguration"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.HybridConnectivity"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.AzureStackHCI"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.Kubernetes"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.KubernetesConfiguration"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ExtendedLocation"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ResourceConnector"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.HybridContainerService"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.Attestation"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.Insights"
    Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
    ```

    ![](./media/lab02a3.png)

    ![](./media/lab02a4.png)

1. Navigate to the **Azure portal**, in search bar type **Subscriptions (1)** and select **Subscriptions (2)** under the services. 

    >**Note:** Ensure that you select the subscription whose name begins with **Azure HOL - xxxxxx**

    ![](./media/Ex2-0.png)

1. Select the **available** subscription.

    
    ![](./media/Ex2-1.png)

1. From the left navigation pane, select **Resource providers (1)** under Settings.

1. In the search bar of the Resource Providers page, search for **Hybrid (2)**, select **Microsoft.HybridCloud (3)**, then click on **Register (4).**

    >**Note:** You can verify all Resource Providers here to ensure they are registered successfully.

    ![](./media/Ex2-2.png)

1. Similarly, on the Resource Providers page, search for **Hybrid**, select **Microsoft.HybridContainerService**, then click on **Register.**

    >**Note:** If it is already registered, please ignore. You can verify all Resource Providers here to ensure they are registered successfully.

    ![](./media/Ex2-14.png)     

1. In the **Azure portal** search bar, type **Microsoft Entra ID (1)** and select **Microsoft Entra ID (2)** under the services. 

    ![](./media/Ex2-3.png)

1. On the Microsoft Entra ID page, from the left navigation pane, select **Enterprise applications** under Manage.

    ![](./media/Ex2-4.png)

1. On the **Entrprise applications | All applications** page, click on **X** to clear the filter **Application type == Enterprise Applications**.

    ![](./media/Ex2-5.png)

1. On the **Entrprise applications | All applications** page, search for **HCI (1)**, then select **Microsoft.AzureStackHCI Resource Provider (2)**.

    ![](./media/Ex2-6.png)

1. On **Microsoft.AzureStackHCI Resource Provider | Overview** page, click on the **copy icon** to copy the **Object ID** and  paste it into Notepad, as you need this in further steps.

    ![](./media/Ex2-7.png)

1. Open and review the **azlocal.json** and **azlocal.parameters.json files** in **VSCode**. Verify that the **azlocal.parameters.json file** looks correct without **"-staging"** placeholder parameter values. This has already been generated with the script available in the Localbox folder.

     ![](./media/Ex2-12.png)

1. Open **azlocal.parameters.json (1)** file, scroll down to see **hciResourceProviderObjectID**, paste the Object ID in **value (2)** that you copied in Step 11 as shown below.

    ![](./media/Ex2-8.png)

1. Press **Ctrl + S** to save the file.

## Task 2: Validate and deploy the Azure Local using PowerShell

In this task, you will validate and deploy the Azure Local cluster using PowerShell with the pre-configured ARM template and parameter files.

1. Open Windows PowerShell ISE window and run the command below to validate your Azure Local deployment and cluster.

   ```
   $TemplateFile = Join-Path -Path $env:LocalBoxDir -ChildPath "azlocal.json"
   $TemplateParameterFile = Join-Path -Path $env:LocalBoxDir -ChildPath "azlocal.parameters.json"
    
   New-AzResourceGroupDeployment -Name 'localcluster-validate' -ResourceGroupName $env:resourceGroup -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParameterFile -OutVariable ClusterValidationDeployment -ErrorAction Stop
   ```

1. The above command will take approximately 45 minutes to get your deployment validated and show you the Azure Local cluster on the Azure Portal.

    >**Note:** If the deployments fail, run the below commands and then restart the VM and again run the code in **Step-1**

    ```
    Install-WindowsFeature -Name Hyper-V,Failover-Clustering,RSAT-Clustering-PowerShell -IncludeManagementTools
    ```

1. You can navigate to the Azure Portal and see a new Azure Local resource created in your resource group.

1. Once the validation is completed, run the command below to start the creation of Azure Local. This command will take approximately 3 hrs to deploy your cluster. 

   ```
   New-AzResourceGroupDeployment -Name 'localcluster-deploy' -ResourceGroupName $env:resourceGroup -TemplateFile $TemplateFile -deploymentMode "Deploy" -TemplateParameterFile $TemplateParameterFile -OutVariable ClusterDeployment -ErrorAction Stop
   ```

1. Once the deployment starts, you can navigate to the **Azure Portal**, in the search bar type **Resource groups (1)** and select **Resource groups (2)** under the services. 

    ![](media/Ex3-0.png)

1. From the Resource groups pane, click on **Azure-Local** resource group and verify the resources present in it.

    ![](media/azurestackhci-rga.png "Select Azure Local Resource Group")

1. In the  **Azure-Local** resource group in the search bar search for **localboxcluster** **(1)** and select **localboxcluster** **(2)** Azure Local.

    ![](media/E2T2S7.png)
   
1. Select the  **Deployments (1)** under Settings to see your **deployment status (3)**. Azure Local may take 3 to 5 hours to get deployed. If you navigate elsewhere in the Azure Portal, you can return to monitor progress on the Deployments tab of the cluster resource. Click **Refresh (2)** to get the latest status on deployment.

     ![](./media/NewEx3-6.png)

## Summary

In this exercise, you assigned Azure Arc permission to the Azure Local resource provider, created and reviewed the generated ARM template, and validated and deployed the Azure Local cluster using the Azure portal.

### You have successfully completed the lab. Click on Next >> to proceed with the next exercise.

![](./media/pg-03.jpg)
