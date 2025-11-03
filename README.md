# Live-traffic-Honey-Pot-Azure-SOC-Lab
<img width="1364" height="755" alt="image" src="https://github.com/user-attachments/assets/ed182e6a-c785-46c6-8b3c-526b9f135b00" />

# Introduction
In this lab, I will deploy a mini honeynet in Microsoft Azure and expose it to the internet so that adversaries can target a deliberately vulnerable virtual machine. During attacks, we will ingest attacker logs into an Azure Log Analytics workspace. These logs will be analyzed with Microsoft Sentinel to generate attack maps and identify the geographic origins of the attacks.

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


