# Exercise 4: Managing AKS on Azure Local 

### Estimated Duration: 60 Minutes

## Overview

In this exercise, you'll be focusing on managing Azure Kubernetes Service (AKS) on Azure Local, which involves creating a logical network specifically tailored for AKS on Azure Local. It also covers setting up an Azure Active Directory (AAD) tenant group for authentication purposes. The process involves deploying AKS on Azure Local via the Azure Portal and establishing the necessary connections to access the AKS deployment. This hands-on lab demonstrates the setup and configuration steps required for deploying and managing AKS in an Azure Local environment.

## Objectives

You will be able to complete the following tasks:

- Task 1: Create a Logical Network for Azure Local for AKS
- Task 2: Create an Entra Group for the authentication of AKS
- Task 3: Create AKS on Azure Local using Azure Portal
- Task 4: Connecting to the Azure Local AKS

## Task 1: Create a Logical Network for Azure Local for AKS

In this task, you will create a logical network in the localboxcluster to provide IP addressing, VLAN, and gateway configuration for AKS deployment.

1. On the **Azure portal**, in search bar type **Resource groups (1)** and select **Resource groups (2)** under the services. 

   ![](media/Ex3-0.png)

2. From the Resource groups pane, click on **Azure-Local** resource group and verify the resources present in it.

   ![](media/azurestackhci-rga.png "Select Azure Local Resource Group")

3. In the  **Azure-Local** resource group in the search bar search for **localboxcluster** **(1)** and select **localboxcluster** **(2)** Azure Local.

   ![](media/Ex3-1.png)

4. In the **localboxcluster** Azure Local, from the left menu select **Logical networks** **(1)** under Resources, and click on **+ Create logical network** **(2)**.

   ![](media/Ex3-2.png)

5. In the **Create logical network** tab, under Basic, fill in the following details and click on **Next: Network Configuration** **(5)**.

    - Subscription: Default subscription **(1)**
    - Resource group: **Azure-Local** **(2)**
    - Logical network name: **localbox-aks-lnet-vlan110** **(3)**
    - Virtual switch name: **ConvergedSwitch(compute_management_storage)** **(4)**

      ![](media/Ex4-0.png)

6. In the **Network Configuation** tab, under the fallowing deatils and click on **Next: Tags** **(9)**.

    | **Variables**                | **Values**                                                    |
    | ---------------------------- |---------------------------------------------------------------|
    | IP address assignment | **Static** **(1)** |
    | IPv4 address space    | **10.10.0.0** **(2)** from the drop down  address prefix select **\24** **(3)** |
    | IP pools              | Enter Start IP **10.10.0.101** **(4)** and End IP **10.10.0.199** **(5)** |
    | Default Gateway       | Enter Default Gateway address as **10.10.0.1** **(6)** |
    | DNS Servers           | Enter DNS Servers **192.168.1.254** **(7)** |
    | VLAN ID               | Enter **110** **(8)** | 

   ![](media/logic-1network-network.png)

7. In the **Tag** tab, leave it as default and click **Next: Review + Create**.

8. In the **Review + Create** tab, click on on **Create** button.

   ![](media/Ex4-2.png)

## Task 2: Create an Entra Group for authentication of AKS

In this task, you will configure an Entra ID group to manage authentication and authorization for AKS.

1. In the Azure portal, click on the search blade at the top and search for **Microsoft Entra ID (1)** and select **Microsoft Entra ID (2)**.

    ![](./media/Ex2-3.png)

1. On the **Microsoft Entra ID** Overview page, click on **+ Add (1)**, then select **Group (2)** from the list. 

    ![](media/Ex3-3.png)

1. In the **New Group** tab, enter the **Group name** as **aks-auth** **(1)**, click on the **No Owner Selected** **(2)** under **Owners**, from the search **(3)** and select **(4)** for user **<inject key="AzureAdUserEmail"></inject>**, and click on **Select** **(5)**.

    ![](media/createnewgroup.png)

1. Click on the **No members Selected** **(1)** under **Members**, from the search **(2)** and select **(3)** for user **<inject key="AzureAdUserEmail"></inject>**, and click on **Select** **(4)**.

   ![](media/E4T2S4.png)

1. In the **New Group** tab, click on **Create** button.

    ![](media/newgroupcreate.png)

## Task 3: Create AKS on Azure Local using Azure Portal

In this task, you will deploy an AKS cluster on Azure Local with Entra-based RBAC authentication and network integration.

1. In the Azure portal, click on the search blade at the top and search for **Azure Arc (1)** and select **Azure Arc (2)**

   ![](media/E4T3S1-1.png)

1. In the Azure Arc page, select **Kubernetes Clusters** under Infrastructure from the left side menu.

   ![](media/E4T3S1.png)

1. In the **Kubernetes Clusters** tab, click on **+ Onboard/Create** **(1)** and from the drop-down select **Create a new Kubernetes cluster (2)**.

   ![](media/E4T3S3-1.png)

1. In the **Create a Kubernetes cluster** tab, fill in the following details in the Basic section and click on **Next: Node Pool** **(8)**.

   | **Variables**                | **Values**                                                    |
   | ---------------------------- |---------------------------------------------------------------|
   | Subscription | Default subscription **(1)** |
   | Resource group | From the drop-down Select **Azure-Local** **(2)**  |
   | Kubernetes cluster name | Enter the cluster name as **localaks** **(3)** |
   | Custom location | From the drop-down Select **jumpstart** **(4)** |
   | Kubernates version | Leave it as default **(5)** |
   | Node size | From the drop down select **Standard_A2_v2** **(6)** |
   | Key pair name | Enter the Key pair name as **localaks** **(7)** |

   ![](media/image-3a-1.png)

1. In the **Node Pool** tab, leave it default and click in **Next: Access**.

1. In the **Access** tab, select **Authentication and Authorization** method as **Microsoft Entra authentication with Kubernetes RBAC** **(1)**, and Click on **Choose Microsoft Entra group** **(2)**. 

   ![](media/aksauth.png)

1. In the **Choose Microsoft Entra group for cluster-admin ClusterRoleBinding** pop-up select **aks-auth (1)** group and click on **Select (2)**.

   ![](media/select-group.png)

1. In the **Access** tab, click on **Next: Networking**.

1. In the **Networking** tab, select **Logical network** as **localbox-aks-lnet-vlan110** **(1)**, enter **Control plane IP** as **10.10.0.5** **(2)**, and click on **Review + create** **(3)** .

   ![](media/Ex3-7.png)

1. In the **Integration tab**, click on **Review + create**.

1. In the **Review + create** tab, click on **Review**.

   ![](media/Ex3-8.png)

   >**Note:** The deployment may take around 30-40 minutes to succeed. 

1. Once the deployment is completed, in the search bar type **Kuberante azure arc (1)** and select **Kuberantes Azure Arc (2)**.

   ![](media/E4T3S12.png)

1. Click on **localaks** to view details such as the Kubernetes version. "Status" may show connecting for some time while the cluster fully connects to Azure.

     ![](media/Ex4-3-1.png)
    
## Task 4: Connecting to the Azure Local AKS

In this task, you will connect to the newly created AKS cluster from the Localbox-Client VM using Azure CLI and kubectl.

1. On the Localbox-Client VM, search **Windows PowerShell (1)** and select **Windows PowerShell (2)**, right-click, and select **Run as Administrator (3)**. Then execute the following command in PowerShell
     
     ![](media/windows-powershell.jpg)

    ```
    az extension add -n connectedk8s
    az extension update --name connectedk8s
    az connectedk8s proxy -n localaks -g azure-local
    ```
     ![](media/Ex4-4.png)
   
   >**Note:** If you get any option to install any extension, please enter **Y**.    

3. From Localbox-Client VM, open a new PowerShell session, and then in the new shell, you will have kubectl access to your cluster. Try running some kubectl commands for yourself.

    ![](media/kubconnected.png)

## Summary

In this exercise, you created a Logical Network for Azure Local for AKS, created an Entra Group for authentication of AKS, created AKS on Azure Local using Azure Portal, and connected to the Azure Local AKS.

### You have successfully completed the exercise. Click on Next >> to proceed with the next exercise.

![](./media/pag-05.jpg)



