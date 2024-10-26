# Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone
In this lab, I will deploy an Azure Storage Account and configure a secure Azure File Share. This file share will be inaccessible from the internet, as I will implement security measures over the Azure Backbone Network, allowing secure access through a dedicated private subnet that hosts a secure virtual machine (VM).

Additionally, I will set up a public subnet with another VM to verify that my file share remains inaccessible from the public internet.

# Key Objectives

    🌐 Create a Virtual Network
    🏗️ Add a Subnet to the Virtual Network and Configure a Storage Endpoint
    🔒 Configure a Network Security Group to Restrict Access to the Subnet
    💻 Set Up a Network Security Group to Allow RDP on the Public Subnet
    📁 Create a Storage Account with a File Share
    🖥️ Deploy Virtual Machines into the Designated Subnets
    ✅ Test the Storage Connection from the Private Subnet to Confirm Access is Allowed
    🚫 Test the Storage Connection from the Public Subnet to Confirm Access is Denied

# NETWORK TOPOLOGY

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/Net-topo.jpg)

## STEP 1: Create and Configure a Virtual Network (VNet): 
- First we create a Resource Group ***Aurora-RG***
- Then created a Virtual Network ***Aurora-Vnet*** This will facilitate all network communications between my resources.

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191796_y.jpg)

## Next: Create Subnets and Network Security Groups
- First, Create a public subnet ***Aurora-Public***

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191798_w.jpg)

- Repeat the same step and create second subnet ***Aurora-Private***
- Review and Create the virtual Network

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191799_y.jpg)

## STEP 2: Configure a network security group to restrict access to the subnet.
- Create a Network Security Group (NSG) with two outbound security rules (Storage and internet) and one inbound security rule (RDP), to enable remote login.
- I will then associate the NSG with the Private subnet. This will restrict outbound traffic from Azure VMs connected to that subnet.

### First, Search and select Network Security Group
- Create New Network Security Group

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191800_w.jpg)

### Now, create an outbound security rule that allows communication to the Azure Storage service.
- On the Aurora-NSGPrivate blade, in the Settings section, click Outbound security rules
- Add New Rule
- Allow Outbound traffic from the private Networks to the Azure storage account

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/nsgprivate.jpg)

### Repeat the above steps and create a new Outbound rule for Outbound Internet Traffic
- On the Aurora-NSGPrivate blade, in the Settings section, click Outbound security rules
- Add New Rule
- Deny Outbound traffic from the private Networks to the Internet

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/Internet.jpg)

### Next, Create an inbound security rule that allows Remote Desktop Protocol (RDP) traffic to the Subnet.
- The rule overrides a default security rule that denies all inbound traffic from the internet.
- Remote Desktop connections are allowed to the subnet so that connectivity can be tested in a later step.
- **Now**, Navigate to the inbound pane, and add a new rule.
- Allow Remote Desktop Connection to the Private Subnet ***Aurora-Private***

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191802_w.jpg)

### NOW, I’ll apply the Network Security Group firewall rules that we’ve created to the Private Subnet.
- On the Subnets pane of the NSG blade, select + Associate,
- And select the Private subnet.

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191804_w.jpg)

### NEXT: Configure a network security group to allow rdp on the public subnet.
- create a Network Security Group (NSG), with one inbound security rule, to allow (RDP).

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191806_w.jpg)

- Associate the (NSG) with the Public subnet. This will allow RDP access to the Public Subnet ***Aurora-Public***.
- In the Azure Portal, Search Network Group, and click create.
- On the Subnets pane of the NSG blade, select + Associate,
- And select the Private subnet.

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191807_w.jpg)

## STEP 3: Create a Storage Account with a File Share, and obtain the storage account key.
This key will enable me to securely access this File Share from the Private Subnet.

- First, in the Azure Portal, search Storage accounts and press the Enter key.
- On the Storage accounts blade, click + Create
- Validate details and create

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/Storage.jpg)

**Next,** On the storage account Overview blade, click File Shares under the Data storage tab, 
- Then click + File Share.
- On the New file share blade, create new file share.

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191810_w.jpg)

**Next,** Retrieve and record the PowerShell script that creates a drive mapping to the Azure file share.
- On the storage account blade, in the list of file shares,
- Click Aurorafile-share.
- On the Aurorafile-share blade, click Connect.

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/FShareScript.jpg)

**Take Record of this script,** You’ll need this in a later in this lab in order to map the file share from the Azure virtual machine on the Private subnet.

- Now, Navigate back to the storage account blade, then in the Security + networking section, click Networking.

- Under Firewalls and virtual networks blade,
- Select the Enabled from selected virtual networks and IP addresses option and click the + Add existing virtual network link.

- link the Private Subnet to this File Share, this ensures that It’s in the Private Subnet, to enable Secure Connection over the Azure Backbone Network.

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileshareNet.jpg)

## STEP 4: Deploy virtual machines into the designated subnets
- Here we will deploy Two Virtual Machines (VM) to each of our Subnets, to test our Storage Account File Share.
- One of them will be designated to the Private Subnet, and the second one will be in the Public Subnet.
- The VM in the Private Subnet should be the only VM that can access this File Share.

**First,** search Virtual Machines in Azure Portal, and Enter.
- click create.
- configure Private VM ***AuroraVM-Private***, and deploy to the Private Subnet ***Aurora-Private***.

