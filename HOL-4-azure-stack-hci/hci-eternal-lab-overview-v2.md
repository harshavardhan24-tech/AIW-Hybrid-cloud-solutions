# Azure Local - Hands on lab

### Overall Estimated Duration: 4 Hours

## Overview

**LocalBox** is a turnkey solution that delivers a fully integrated sandbox for exploring **Azure Local** capabilities and hybrid cloud integration within a virtualized environment. Purpose-built to operate entirely within a single Azure subscription and resource group, LocalBox provides a streamlined way for users to gain hands-on experience with **Azure Local** and **Azure Arc** technologies without requiring dedicated physical hardware. 

With the general availability of **Azure Local 24H2**, the platform introduces simplified configuration and deployment of Azure Local instances, including enhanced support for virtual machine (VM) self-service management directly through the Azure portal. LocalBox has been updated to align with this release, offering Azure Local instances based on the new 24H2 operating system. Previous versions of Azure Local are no longer included in LocalBox, ensuring users always work with the latest capabilities.

## Azure Local capabilities available in LocalBox

### 2-node Azure Local instance

LocalBox automatically creates and configures a two-node Azure Local instance using nested virtualization with Hyper-V running on an Azure Virtual Machine. This Hyper-V host creates three guest virtual machines: two Azure Local machines (_AzLHOST1_, _AzLHOST2_), and one nested Hyper-V host (_AzLMGMT_). _AzLMGMT_ itself hosts two guest VMs: an [Active Directory domain controller](https://learn.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview), and a [Routing and Remote Access Server](https://learn.microsoft.com/windows-server/remote/remote-access/remote-access) acting as a virtual router.

![Screenshot showing LocalBox nested virtualization](media/nested_virtualization2.png)

### Virtual machine management

LocalBox comes with [guest VM management in Azure portal](https://learn.microsoft.com/azure/azure-local/manage/azure-arc-vm-management-overview). The LocalBox documentation will walk you through how to use this feature, including configuring VM images from the Azure marketplace and creating VMs on your instance.

### AKS enabled by Azure Arc on Azure Local

Azure Local includes [AKS enabled by Azure Arc](https://learn.microsoft.com/azure/aks/aksarc/aks-overview) as part of the default configuration. A user script is provided that can be used to create a workload instance.

## LocalBox Azure Consumption Costs

LocalBox resources incur Azure consumption charges based on the underlying services, such as compute, storage, networking, and other associated components. These costs may vary depending on the Azure region where LocalBox is deployed. To avoid unnecessary charges, it's important to monitor your LocalBox deployments and disable or delete resources when they are not in use. Please see the [Jumpstart LocalBox FAQ](https://jumpstart.azure.com/azure_jumpstart_localbox/faq) for more information on consumption costs.

## Objective

To create a flexible and cost-effective hybrid cloud environment that seamlessly integrates on-premises and cloud resources, enabling organizations to optimize performance, streamline management, and enhance scalability while ensuring security and compliance. This approach allows businesses to leverage existing investments, modernize applications, and improve disaster recovery and backup capabilities, ultimately driving innovation and agility in a rapidly evolving digital landscape.

- **Preparing the environment with the prerequisites to deploy Azure Local:** Ensure the infrastructure meets all requirements for a successful Azure Local deployment, enabling efficient resource utilization and performance.
  
- **Deploying JumpStart-LocalBox in Azure Portal:** Quickly provision a ready-to-use Azure Local environment using JumpStart-LocalBox for streamlined setup, accelerating time to value for cloud initiatives.

- **Verify the JumpStart Local Box deployment:** Confirm the successful deployment of the JumpStart Local Box to ensure readiness for subsequent configurations, minimizing potential issues during production rollout.
  
- **Azure Backup Server on Azure Local:** Implement Azure Backup Server to enhance data protection and recovery capabilities within Azure Local, ensuring business continuity and compliance with data retention policies.
  
- **Managing AKS on Azure Local:** Oversee and optimize Azure Kubernetes Service (AKS) deployments on Azure Local for efficient container orchestration, facilitating rapid application development and deployment.
  
- **Azure Local machines Provisioning:** Facilitate the rapid creation and deployment of virtual machines within the Azure Local environment, enhancing operational efficiency and resource allocation.
  
- **Azure Local Update management using Azure Portal:** Streamline the update management process for Azure Local through the Azure Portal for improved system reliability and security, ensuring the infrastructure is always up-to-date with the latest features and patches.

## Prerequisites

Participants should have:

- **Basic Cloud Knowledge:** Understanding of cloud computing concepts, including IaaS, PaaS, and SaaS.
- **Familiarity with Azure Services:** Basic knowledge of Azure services and the Azure Portal interface.
- **Networking Fundamentals:** Understanding of networking concepts, such as IP addressing, subnets, and routing, which are crucial for configuring hybrid environments.
- **Windows Server Knowledge:** Proficiency with Windows Server, including installation, configuration, and management, as Azure Local runs on Windows Server technology.
- **Virtualization Concepts:** Familiarity with virtualization technologies, including Hyper-V, as Azure Local utilizes Hyper-Converged Infrastructure.
- **PowerShell Basics:** Basic knowledge of PowerShell for scripting and automation tasks in Azure and Azure Local environments.
- **Storage Fundamentals:** Understanding of storage technologies and concepts, including SAN, NAS, and local storage, which are relevant to Azure local setups.
- **Backup and Disaster Recovery Concepts:** Awareness of backup strategies and disaster recovery planning to effectively implement Azure Backup Server.
- **Kubernetes Basics:** Familiarity with containerization and Kubernetes concepts, especially for managing AKS on Azure Local.

## Architechture

The architecture of a Hybrid Cloud Solution using Azure Local integrates on-premises infrastructure with Azure services, creating a cohesive and flexible environment. At the core, Azure Local utilizes a hyper-converged infrastructure powered by Windows Server and Hyper-V, enabling efficient virtualization and storage management through Storage Spaces Direct. This on-premises setup connects seamlessly to Azure services via the Azure Portal, allowing organizations to leverage cloud capabilities such as Azure Backup, Azure Kubernetes Service (AKS), and Azure Site Recovery. Management and monitoring are facilitated through Windows Admin Center, providing a unified interface for performance tracking and configuration. The architecture supports a hybrid model that ensures data locality, optimized workload placement, and enhanced disaster recovery, empowering businesses to scale their operations while maintaining control over their data and resources.

## Architecture Diagram

![](./media/architecturelocal.png)

## Explanation of Components

The architecture for this lab involves the following key components:

- **Azure Local:** The primary infrastructure service providing a hyper-converged environment that integrates with Azure.
- **Azure Backup:** A service that provides backup and disaster recovery capabilities for on-premises and cloud-based resources.
- **Azure Kubernetes Service (AKS):** A managed container orchestration service that can run on Azure Local for deploying and managing containerized applications.
- **Azure Monitor:** A service for monitoring application performance and infrastructure health, offering insights into the operation of Azure Local environments.
- **Azure Site Recovery:** A disaster recovery service that can be used to replicate on-premises workloads to Azure for business continuity.
- **Windows Server:** The underlying operating system that runs Azure Local, providing the necessary virtualization capabilities.
- **Hyper-V:** The virtualization technology used in Azure Local to host virtual machines.
- **Windows Admin Center:** A management tool for configuring and monitoring the Azure Local cluster and its resources.

## Getting Started with the Lab
 
Welcome to your Hybrid Cloud Solution - Azure Local Workshop! We've prepared a seamless environment for you to explore and learn about Azure services. Let's begin by making the most of this experience:
 
## Accessing Your Lab Environment

Once you're ready to dive in, your virtual machine and lab guide will be right at your fingertips within your web browser.

### Virtual Machine & Lab Guide
 
Your virtual machine is your workhorse throughout the workshop. The **Guide** is your roadmap to success.

![](./media/Gettingstarted.png "Lab Environment")
 
### Exploring Your Lab Resources
 
To get a better understanding of your lab resources and credentials, navigate to the **Environment** tab.
 
![](./media/env-2309.png "Lab Environment")
 
### Utilizing the Split Window Feature
 
For convenience, you can open the lab guide in a separate window by selecting the **Split Window** button from the Top right corner.
 
![](./media/GettingStarted-2.png)
 
### Managing Your Virtual Machine
 
Feel free to **Start, Restart, or Stop (2)** your virtual machine as needed from the **Resources (1)** tab. Your experience is in your hands!

![](./media/GettingStarted-3.png)

## Lab Guide Zoom In/Zoom Out

To adjust the zoom level for the environment page, click the **A↕: 100%** icon located next to the timer in the lab environment.

![](./media/labzoom-1.png)

## Login to the Azure portal

1. In the **LocalBox-Client** virtual machine, double-click on the **Microsoft Edge** browser shortcut that is provided on the desktop.
  
   ![](./media/azure-portal-edgea.png)
    
1. Navigate to Azure Portal using the URL provided link

   ```
   https://portal.azure.com/
   ``` 

1. On the **Sign in** blade, you will see a login screen, in which enter the following email/username and password and then click on **Sign in**.  

   * **Azure Username/Email**:  <inject key="AzureAdUserEmail"></inject> 

        ![](./media/signin1-0903.png) 

   * **Temperory Access Pass**:  <inject key="AzureAdUserPassword"></inject>
  
        ![](./media/signin2-0903.png)
  
1. If you see the pop-up **Stay Signed in?** click **Yes**.

    ![](./media/g3.png)

1. If a **Welcome to Microsoft Azure** popup window appears, click **Maybe Later** to skip the tour.

    ![](./media/g4.png)

1. On the **Azure portal**, in search bar type **Resource groups (1)** and select **Resource groups (2)** under the services. 

   ![](media/Ex3-0.png)

1. From the **Resource** groups pane, click on the **Azure-Local** resource group and verify the resources present in it.

   ![](media/azurestackhci-rga.png "Select Azure Local Resource Group")

## Support Contact
 
The CloudLabs support team is available 24/7, 365 days a year, via email and live chat to ensure seamless assistance at any time. We offer dedicated support channels tailored specifically for both learners and instructors, ensuring that all your needs are promptly and efficiently addressed.

Learner Support Contacts:
- Email Support: cloudlabs-support@spektrasystems.com
- Live Chat Support: https://cloudlabs.ai/labs-support

Now, click on **Next >>** from the lower right corner to move on to the next page.

![](./media/Next.png)

### Happy Learning!!
