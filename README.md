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

![SOC]()

