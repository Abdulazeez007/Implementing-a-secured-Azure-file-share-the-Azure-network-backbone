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

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-on-the-Azure-network-backbone/blob/main/FileShare/VMPrivate.jpg)

## Now, Follow the same step and create the public VM and connect it to the public subnet
- Search Virtual Machines in Azure Portal, and Enter.
- Click create.
- Configure Private VM ***AuroraVM-Public***, and deploy to the Private Subnet ***Aurora-Public***

## STEP 5
- Test the storage connection from the private subnet to confirm that access is allowed.
- connect to the AuroraVM-Private virtual machine via Remote Desktop (RDP) and map a drive to the Aurorafile-share.

**First,** Navigate to the Virtual machines blade.
- On the Virtual machines blade, click the ***AuroraVM-Private*** entry.
- On the ***AuroraVM-Private*** blade, click Connect and, in the drop down menu, click RDP.
- Click Download RDP File and use it to connect to the AuroraVM-Private Azure VM via Remote Desktop.
- When prompted to authenticate, provide the credentials setup while provisioning the VM.

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-the-Azure-network-backbone/blob/main/FileShare/VMPrivConnect.jpg)

## Next: Map drive (Z) to the Azure File share ***Arurorafileshare*** within the Remote Desktop session to a Windows Server 2022 computer.
- Within the Remote Desktop session to AuroraVM-Private, click Start and then click Windows PowerShell ISE.
- Right click and run as Administrator.
- Within the Windows PowerShell ISE window, open the Script pane,
- Then paste and run the PowerShell script recorded earlier in this lab,

 ***remember?***
 ![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-the-Azure-network-backbone/blob/main/FileShare/VMPrivPS1.jpg)

 **After Running the script** let’s start File Explorer and verify that the (Z:) drive mapping has been successfully created.
 
***And BOOM! Just like magic***

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-the-Azure-network-backbone/blob/main/FileShare/VMPrivFilemount.jpg)

## Next, from the console pane of the Windows PowerShell ISE console
- run the command to verify that the virtual machine has no outbound connectivity to the internet, you know, just to be certain.

      ***Test-NetConnection -ComputerName www.bing.com -Port 80***

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-the-Azure-network-backbone/blob/main/FileShare/VMPrivPSFailed.jpg)

***Failed***! As expected.

- The test will not succeed due to the Network Security Group associated with the Private Subnet restricting outbound access to the Internet.
- At this stage, we can confirm that the virtual machine in the Private Subnet has access to the storage account. But, lacks access to the public internet

## STEP 8: Test the storage connection from the public subnet to confirm that access is denied.

- Connect to the Aurora-VMPublic virtual machine via Remote Desktop (RDP) to test if we can access the File Share from a Public Network over the Internet.
- First, Navigate to the Virtual machines blade.
- On the Virtual machines blade, click the AuroraVM-Public entry.
- On the Aurora-VMPrivate blade, click Connect and, in the drop down menu, click RDP.

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-the-Azure-network-backbone/blob/main/FileShare/VMPubConect.jpg)


- After Connecting to the Public VM, follow the same steps as before to see if I can also Map the Azure File Share over this Network.
- Run the Powershell script

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-the-Azure-network-backbone/blob/main/FileShare/VMPubPSFile.jpg)

***And as Expected, Access Is Denied! This time, I receive the New-PSDrive : Access is denied error.***

**Note: Access is denied because the Aurora-VmPublic virtual machine is deployed in the Public subnet. The Public subnet does not have a service endpoint enabled for the Azure Storage. The storage account only allows network access from the Private subnet.**

**Next,** from the console pane of the Windows PowerShell ISE console, run the following to verify that the virtual machine has outbound connectivity to the internet.

     ***Test-NetConnection -ComputerName www.bing.com -Port 80***

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-the-Azure-network-backbone/blob/main/FileShare/VMPubInternetconnected.jpg)

***The test succeeded because there is no outbound security rule to deny internet on the Public subnet.***

DURING THIS PROJECT WE:

    🌐 Created a storage endpoint to ensure that traffic destined for Azure Storage remains within the Azure backbone network.
    🔒 Developed and configured Network Security Groups (NSGs) for various subnets, allowing for granular control over network interactions and resource access.
    📂 Configured the storage endpoint to restrict access exclusively to resources from a designated subnet.
    🛡️ Securely mounted and mapped Azure File Share on remote VMs using secure Storage Account keys.
    🚫 Verified that resources outside the specified subnet cannot access the storage.

***Thank you for following along! If you made it this far, your attention is greatly appreciated.***

Note: Always remember to delete and deprovision cloud resources when their services are no longer required! 🗑️

![SOC](https://github.com/Virus192/Implementing-a-secured-Azure-file-share-the-Azure-network-backbone/blob/main/FileShare/photo_5827888068391191830_w.jpg)


***SEE YOU AGAIN SOON!!!***
