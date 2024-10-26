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

## STEP 2: Configure and add a Private Subnets to the Virtual Networks
- 

