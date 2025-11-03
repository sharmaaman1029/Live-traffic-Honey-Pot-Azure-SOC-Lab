# Live-traffic-Honey-Pot-Azure-SOC-Lab
<img width="1364" height="755" alt="image" src="https://github.com/user-attachments/assets/ed182e6a-c785-46c6-8b3c-526b9f135b00" />

# Introduction
In this lab, I deployed a mini honeynet in Microsoft Azure and expose it to the internet so that adversaries can target a deliberately vulnerable virtual machine. During attacks, we will ingest attacker logs into an Azure Log Analytics workspace. These logs will be analyzed with Microsoft Sentinel to generate attack maps and identify the geographic origins of the attacks.

The architecture for our lab consists of the following:

- Azure Subscription
- Resource Group 
- Virtual Network
- Virtual Machine (Windows 11)
- Network Security Group
- Log Analytics Workspace
- Microsoft Sentinal (SIEM)

# Attack map within the first 24 hours
<img width="1456" height="657" alt="image" src="https://github.com/user-attachments/assets/d86b2dae-779d-4c4d-80eb-27548324b8e2" />

Within the last 24 hours there were 15995 unauthorized attempts to log into our VM.
Event ID 4625 related to - An account failed to log on.

<img width="1523" height="882" alt="image" src="https://github.com/user-attachments/assets/0883e6a2-b5b7-4130-88ee-5f3d0350406a" />

# Step 1 - Set up your Azure Subscription.
First step is pretty simple, just go to azure and get your free subscription. You can use this link to make an account https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account

If Azure if not letting you make an account, you can go with the pay-as-yo-go subscription but be mindful to turn of your VM's right after you are done with them or else charges might build up.

# Step 2 - Create the honey pot -> Your Windows 11 VM
For this step it is recommended to follow azure's architecture and make a Resource group first which will contain all the components of your project.
Here I created a resouce group RG1
<img width="1898" height="967" alt="image" src="https://github.com/user-attachments/assets/9fdcc42f-a849-483d-bd2e-50661b92b545" />

Make sure to configure a virtual network next..

Finally make your Windows 11 virtual machine and in the resource group select your resource group (whatever you named) in my case RG1 and virtual network as well.

<img width="1902" height="961" alt="image" src="https://github.com/user-attachments/assets/2d37da38-5415-4534-98ad-9d4fa6a1d26a" />

<img width="1903" height="975" alt="image" src="https://github.com/user-attachments/assets/eab9dc72-a4ae-4077-91aa-1e3b3fb3f6f0" />

Next you will go back to your resouce group and select Network Security Group (nsg) which must have been made automatically. In my case it is called CorpVM1-nsg

<img width="1913" height="970" alt="image" src="https://github.com/user-attachments/assets/b5afa22e-a5d0-44de-a743-543db58b8fd9" />

Here you will need to change the inbound rules for your virual machine
Go to inbound rules under settings

<img width="361" height="845" alt="image" src="https://github.com/user-attachments/assets/7d391983-8d3a-4cf5-832f-eef0201baa8b" />

Delete the normal RDP rule and instead add this rule in your list

<img width="721" height="982" alt="image" src="https://github.com/user-attachments/assets/becd56c7-dd5f-475d-a549-7f37d451af8c" /> 
<img width="716" height="977" alt="image" src="https://github.com/user-attachments/assets/e3878a9f-be0c-47e3-9abf-782fa77c37d4" />

This will make our VM super vulnerable and it can accept incoming traffic from the attackers.

Apart from configuring you NSG, you will also need to log into your VM and turn off the windows firewall (start -> wf.msc -> properties -> all off)

Here click on Windows defender firewall properties and turn everything off

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/6cba97f0-b46a-4219-8dcf-65f6f270435b" />
<img width="1304" height="976" alt="image" src="https://github.com/user-attachments/assets/e4ce0dba-e9a8-40ad-9da5-11e5265f9cee" />

Just to confirm that your rules are working try pinging your VM using your own computer's powershell. Grab your VM's IP and use powershell to ping. 

If your shell looks like this you are on the right track.

<img width="1154" height="559" alt="image" src="https://github.com/user-attachments/assets/663c4c91-32c0-46a7-b4b5-f37215b7ca3e" />








