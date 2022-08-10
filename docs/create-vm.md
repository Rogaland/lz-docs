Page last updated: {docsify-updated}

# VM with subnet and NSG

**Target Audience:** IT departments of the schools and external vendors that manages IT systems for Rogaland fylkeskommune.

**Key Objective:** This document will guide you through the setup process for the Virtual Machine and dependent resources from the Azure Portal.

## Before you begin

Sign in to the [Azure portal](https://portal.azure.com/) with your Azure account.

To create the Virtual Machine (VM), we first need to create dependent resources:

- [Network Security Group](https://docs.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview)
  - Must be created first, as you will not be allowed to create a subnet without NSG.
- [Subnet](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-manage-subnet#add-a-subnet)
  - The network boundary for where the VM exists.

## Network Security Group

First we will be making a `Network Security Group` (NSG). This resource works as a software defined firewall and in this resource you will need to define the allowed traffic to your **subnet**.

    NOTE: The NSG should is applied to the subnet, not the individual resource. 
    This imples that all inbound/outbound rules in the NSG will be applied to the entire subnet. 
    Thus your rules should be as specific as possible to avoid allowing unwanted traffic. 

Start by searching for `nsg` in the search field of the portal.

![image](https://user-images.githubusercontent.com/48277303/183627020-491dee9d-11ca-4b86-9546-e68168a57467.png)

Click `Create`

![image](https://user-images.githubusercontent.com/48277303/183627462-92ed6f81-e684-4a97-b745-89281773fd49.png)

Fill in the correct details for your network security group:

- **Subscription**: Name of your subscription
- **Resource group**: lz-network-westeurope
- **Name**: some name that best decribe what the nsg is used for. IE `lz-network-westeurope-vnet-vm-subnet-linux-nsg`

***Best practise: Try to adhere to naming convention by including name of vnet as well.***

Then click `Review + create`

![image](https://user-images.githubusercontent.com/48277303/183914856-53ada3e0-d92d-47df-bc14-e018ec01a8c8.png)
Then wait for the validation to pass and then click `Create`.
The NSG can also be found in the next step of the guide.


![image](https://user-images.githubusercontent.com/48277303/183914812-7259a39a-00c7-4987-9e76-f5e68f61c6eb.png)
After the creation of the NSG, you can `Go to resource` if you want to see the NSG you just created.

## Creating a Subnet

Navigate to the subscription you are working on.

Management groups -> RFK -> Landing Zones -> External -> *your subscription*

![image](https://user-images.githubusercontent.com/48277303/183623731-0d458ec9-4321-42d2-aa00-e609e8bf6944.png)

Rogaland IT should have pre-provisioned a resource group with a virtual network for you in the landing zone.

Navigate to the `Resource Groups` tab of the selected subscription, and then select the `lz-network-westeurope` resource group.

![image](https://user-images.githubusercontent.com/48277303/183624129-336fe6d4-a5f8-40e8-b909-d90c1a21c77c.png)

- Click on the `lz-network-westeurope-vnet` vnet.

- Click on the `Subnets` tab, and then the `+ Subnet`.

![image](https://user-images.githubusercontent.com/48277303/183632615-e3544b7a-d26a-49b4-9ea8-23db03fb88a6.png)

Change the name of the subnet to preferably something similar to the NSG we just created. IE `vm-subnet-linux`-
Chose the NSG we created in the `Network security group` tab of the subnet configuration.

When creating the subnet you will get a suggested range within the virtual network. Make adjustments to the size if required.
**If you are unsure about this step, please consult Rogaland IT.**

Then click `Save` in the bottom.

![image](https://user-images.githubusercontent.com/48277303/183632739-cb173561-efae-46a1-83fc-6991533c3aa9.png)

## Creating the Virtual Machine

Search for Virtual Machines in the search field in the portal and click `Virtual Machines`.

![image](https://user-images.githubusercontent.com/48277303/183652531-875de372-2853-4316-bcb1-e27d70cbaf40.png)

Click `+ Create` and then `Azure virtual machine`

![image](https://user-images.githubusercontent.com/48277303/183653083-37dca100-c8b7-4911-af79-52073d89a520.png)

### Configure the virtual machine basics

- **Subscription**: Name of your subscription
- **Resource group**: `Create new` Add a name here that describes the collection of resources. IE `vm-linux`
  - ***Best practise: The resources within the resource group should have the same lifecycle. IE. Created together and deleted together.***
- **Virtual machine name**: Name of the VM
  - Note: Strict limitations in naming inside guest OS.
    - Maximum characters:
      - Windows: 15
      - Linux: 64
    - Example: p-hyperion-app1
- **VM image and size**: Here you need to chose which image and size you want the VM to be. [VM Size overview](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes)
- **Admin account**: Chose a username and password, used for logging in to the VM.
  - ***Best practice: Create an Azure Key Vault and store the username and password as secrets***

![image](https://user-images.githubusercontent.com/48277303/183914119-78c8450f-3c0c-4c4f-8a2d-d019a20ca8e7.png)


#### Configure the virtual machine disks

- The OS disk size defaults to 32GB for linux and 128GB for Windows.
- Depending on the chosen VM size/sku you may get an additional temp disk. This is a locally attached disk that is great for **temporary** storage.
  - IMPORTANT: Data stored on temp disk will be irreversably lost when the VM restarts.

**Best practice: Store your application on a separate data disk. This will let you scale disk size/performance individually.**

[Read more](https://docs.microsoft.com/en-us/azure/virtual-machines/managed-disks-overview)

![image](https://user-images.githubusercontent.com/48277303/183845432-1ca2af6d-5800-45e0-8977-dc24d401146f.png)

#### Configure the virtual machine network

![image](https://user-images.githubusercontent.com/48277303/183845750-381444d1-42ea-4b3b-99aa-11ec1dd395b7.png)

Change more of the settings if needed, when done click `Review + create`, wait for verification of the VM, if successful you get a overview of the VM. If this looks good, you click the `Create` button to deploy the VM.'

If you are unsure of any of these steps, try the *Useful links* below, or consult with Rogaland IT for assistance.

### Useful links

- MS Docs: [Network Concept and Best practises](https://docs.microsoft.com/en-us/azure/virtual-network/concepts-and-best-practices)
- MS Learn: [Create Windows VM](https://docs.microsoft.com/en-us/learn/modules/create-windows-virtual-machine-in-azure/)
- MS Learn: [Create Linux VM](https://docs.microsoft.com/en-us/learn/modules/create-linux-virtual-machine-in-azure/)
