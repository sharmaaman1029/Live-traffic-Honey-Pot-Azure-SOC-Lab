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

# Step 3 - Logging into your VM and inspecting logs

Next I am going to fail 3 login attemps into my VM while doing the RDP, the same thing attackers will be doing when they see our VM online.

<img width="561" height="648" alt="image" src="https://github.com/user-attachments/assets/7e93f449-3122-4b96-af4b-53b042a6699e" />

You can go to event viewer on you Windows VM and see the log inside Windows logs -> security and see the logs with the event ID of 4625 which as per windows is "This event is generated when a logon request fails. It is generated on the computer where access was attempted."

<img width="1866" height="1122" alt="image" src="https://github.com/user-attachments/assets/20a42bd7-ddeb-43f7-9566-15831e81f858" />

Now we will create a central log repository where all of these logs can be directed for us on Azure.

# Step 4 - Setting up Log Analytics Workspace 

To start up with this part first we will need to create a Log analytics workspace. Simply go to search bar search Log analytics workspace and click create

<img width="1919" height="1091" alt="image" src="https://github.com/user-attachments/assets/4f796481-a4e9-4f7a-93d7-42ff79bebbb0" />

Make sure to use the same resource group as your VM, here I named my Log analytics workspace as LAW1.

<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/babc10e2-518c-4354-8bcd-e2a4c18cb585" />

Next if you go to Microsoft Sential, you should be able to see your LAW1 in the Sentinal.

<img width="1917" height="978" alt="image" src="https://github.com/user-attachments/assets/1644e32d-b9c5-4441-86aa-c1c6e3b46704" />

By doing this, we have connected our LAW to Microsoft Sentinal, but we are still missing a connector between our VM and the LAW.

Now we will install the Windows security event connector - go to your Sentinal instance -> click on the LAW instance -> Go to content hub ( under content management ) -> Search windows security event and click install. (In my case it says manage cause I have already installed it)

<img width="1919" height="976" alt="image" src="https://github.com/user-attachments/assets/f5fca67a-02a7-4944-b932-bea6fc28b618" />

After the installation is complete click manage and select Windows security event via AMA (Azure monitoring agent) and click on open connector page.

<img width="1918" height="975" alt="image" src="https://github.com/user-attachments/assets/de253cc6-b123-4d0e-bf73-af3490d34f07" />

Now we will create a rule which lets our VM to forward all of the log into LAW. It also creates a new object inside our resouce group, which I will show later on.

<img width="1918" height="977" alt="image" src="https://github.com/user-attachments/assets/51045c72-9917-4276-8901-88dfbfb47a94" />

You might see I already have a rule named DCR in the list, I will show you how to create the rule now. 
Click on create data collection rule -> Select your desired name, azure subscription and your resouce group you are using for the VM 

<img width="1919" height="979" alt="image" src="https://github.com/user-attachments/assets/7fe4a7fb-85f4-4c30-aa2b-9eb5e50a5c25" />

In the resouces tab just drop down the menu and select your Azure VM.

<img width="1919" height="981" alt="image" src="https://github.com/user-attachments/assets/7d014adf-6ed8-4c7b-a6d3-7fcd79127325" />

For the collect section select All security events and hit review + create

<img width="1916" height="976" alt="image" src="https://github.com/user-attachments/assets/a6e33d80-8f6d-4032-8604-bc743fbcad60" />

Now if you go back to your VM, go under settings -> extensions + applications you must be able to see your AMA getting installed.

<img width="1916" height="972" alt="image" src="https://github.com/user-attachments/assets/e52143a1-869f-446d-bd95-14a0868e5368" />

Now we can try querying the log within LAW. For that go to your LAW instance and go to logs. Here you might need to select KQL mode instead of simple mode in order to query KQL.

<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/d63fcbb4-3813-4657-a146-7cf07f870413" />

You can use this query to see the failed login attempts into your VM. Just type this query and hit run

SecurityEvent
| where EventID == 4625

If you are unable to see anything, try failing some RDP logins into your VM and you should be able to see those attempts in your logs. If you don't see anything yet, just make sure your AMA for your VM is installed all the way to provisioning succeded.

<img width="1919" height="974" alt="image" src="https://github.com/user-attachments/assets/a2b1f267-5be5-446c-8470-885c8516a2ca" />

We can query the log from LAW and the Sentinal both.

After you are done with this step, make sure to leave your VM running for atleast 1-2 hours so attackers can see your VM and you can start getting those juicy logs inside your LAW and Sentinal.

# Step 5 - Enriching your logs and finding location data.

Now our VM will be attacked by hackers from around the world, and their IP addresses will be recorded in our logs. To plot those IP addresses on a map and see which parts of the world are targeting our VM, we need geographic data that maps each IP block to a location. We will use the file geoip-summarized.csv to resolve IP addresses to physical locations for mapping.

If we observe the SecurityEvent logs in the Log Analytics Workspace; there is no location data, only IP address, which we can use to derive the location data.
<img width="1456" height="630" alt="image" src="https://github.com/user-attachments/assets/d6a9352a-9937-4cc0-a331-a614d96d0e1b" />

Next we are going to import a spreadsheet geoip-summarized.csv, referred to as a “Sentinel Watchlist,” which contains geographic information for each block of IP addresses.

So go to sentinal click on your LAW and then go to Watchlist right under configuration and click on the link for the defender portal

<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/00deab6b-3a97-4b19-9b0c-37896d0be6d7" />

Repeat the same steps, go to Microsoft sentinal -> Configuration -> Watchlist -> click +New

<img width="1919" height="985" alt="image" src="https://github.com/user-attachments/assets/5ac24af2-6200-4283-b68c-7394d88d9ba1" />

Give your preferred name for the watchlist and alias

<img width="1919" height="958" alt="image" src="https://github.com/user-attachments/assets/495becac-a3ef-4058-9ed9-7bd4c163256c" />

Next download the geoip-summarized.csv file from above and upload it here. For the search key select Network and hit review + create.

<img width="1894" height="969" alt="image" src="https://github.com/user-attachments/assets/12d1ef41-5352-4993-a949-69b110ba3d6e" />

Once you are done, it will dump you back to the watchlist page and you will be able to see your watchlist in the menu. Let it upload until it is like 100%.

<img width="1895" height="974" alt="image" src="https://github.com/user-attachments/assets/fa560186-8af6-48f4-96f8-bc1ffd8fe0db" />

Now as our watchlist is inside Azure, we can use it to resolve any IP address of an attacker to see what is it's gergraphical location. For example - Let me choose one single attacker's IP address from our LAW log.

<img width="1067" height="731" alt="image" src="https://github.com/user-attachments/assets/09a053ee-5cd4-40f5-9892-4212c6adbc1b" />

Here I want to see where is the IP address 162.12.208.107 from. We can use the following KQP query to resolve the IP using our watchlist.

Here in the log we can see that it is from Moscow, Russia. We even get the longitude and latitude from our watchlist.

<img width="1102" height="740" alt="image" src="https://github.com/user-attachments/assets/746caec3-66b7-4620-bb17-ce342e93ff22" />

# Step 6 - Creating The Attack Map

Now using our watchlist, logs and sentinal we will create a attack map which clearly depicts where the attack is coming from.

Go to your Sentinal instance and click on Workbooks under Threat Management and then click on the Defender portal link.

<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/345a43b3-969b-4c4f-9431-39cc01703629" />

Under Microsoft Sentinal -> Threat Management -> Workbooks -> click on + Add workbook

<img width="1919" height="1032" alt="image" src="https://github.com/user-attachments/assets/3c3f1441-374f-4096-acf2-440ca196e72c" />

Here edit and remove the sample elements and click on add source + visualization.

<img width="1916" height="1020" alt="image" src="https://github.com/user-attachments/assets/576bac3f-1267-435f-9d25-43780189043c" />

Here go to the advanced editor option, remove the given code and paste the json code given above. ( file is called map ). Hit Apply and click on done editing.

<img width="1919" height="975" alt="image" src="https://github.com/user-attachments/assets/e8e66ce8-5fbe-4844-a760-019ff753230b" />

And with that your attack map will be created 

<img width="1918" height="970" alt="image" src="https://github.com/user-attachments/assets/227f67d0-a8d4-4728-8e19-b32b73108ac6" />

You can also change the Map settings using the edit -> Visual formatting and we have whole bunch of map setting you can play with.

<img width="1899" height="977" alt="image" src="https://github.com/user-attachments/assets/4dc3fae5-8843-457e-8b0c-961713441d16" />

# So this is how I created a live honey net using a Azure VM and then made a attack map using Azure Sentinal. 

A huge shoutout to Josh Madakor - who created the live demo od this project. Do make sure to check his video out on YT. It is really detailed and I really like how he explains everything so well.
https://www.youtube.com/watch?v=g5JL2RIbThM&t=3438s








