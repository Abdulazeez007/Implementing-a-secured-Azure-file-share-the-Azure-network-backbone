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

### Next, reate an inbound security rule that allows Remote Desktop Protocol (RDP) traffic to the Subnet.
- The rule overrides a default security rule that denies all inbound traffic from the internet.
- Remote Desktop connections are allowed to the subnet so that connectivity can be tested in a later step.
- **Now**, Navigate to the inbound pane, and add a new rule.
- Allow Remote Desktop Connection to the Private Subnet ***Aurora-Private***

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191802_w.jpg)

### NEXT I’ll apply the Network Security Group firewall rules that we’ve created to the Private Subnet.
- On the Subnets pane of the NSG blade, select + Associate,
- And select the Private subnet.

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191804_w.jpg)

### NEXT: Configure a network security group to allow rdp on the public subnet.
- create a Network Security Group (NSG), with one inbound security rule, to allow (RDP).

![SOC]()

- Associate the (NSG) with the Public subnet. This will allow RDP access to the Public VM I’ll use for testing later on.

- In the Azure Portal, Search Network Group, and click create.

- Configure NSG, like we did earlier on, but this time for the Public Subnet.

