<h1>🗺️ SOC Project - Azure SIEM </h1>

<h2>Description</h2>
Deploy a honeypot to attract various threat actors. After deployment, connect the honeypot event logs to Microsoft Sentinel, a Security Information and Event Management (SIEM) tool. Within the SIEM, perform an in-depth analysis of the attacks using an attack map. <br />


<h2>Utilities Used</h2>

- <b>Microsoft Azure </b> 
- <b>PowerShell </b>
- <b>Microsoft Sentnel (SIEM)</b>
- <b>Remote Desktop Connection </b>

<h2>Enviroments Used</h2>

- <b> Microsoft Azure </b>
- <b> Windows 11 Operating System </b>

<h2> SOC Project Journey</h2>

<p align="center">
To begin the process, I used Microsoft Azure to create a resource group. Within this group, I set up a virtual network, virtual machine, log analytics, Microsoft Sentinel, and other necessary components. The resource group was named Homelab-SOC-LAB. 
<br/>
<img src="https://imgur.com/QzNvpI7.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
<p align="center">
After creating the resource group, I proceeded to create a virtual network and a virtual machine. The virtual network was named VNET-SOC-LAB. During the creation process, I encountered an issue where the VNET failed to deploy because it was not in the same region as the resource group. Once this was corrected, the deployment was successful.
<p align="center">
Next, the virtual machine (VM) was created and will be configured to function as the honeypot. To make it more enticing to attackers, the VM was renamed to COMP-USER1. The previously created VNET was then assigned to the VM, followed by the installation of Windows 11 and the necessary hardware configurations.
<br/>
<img src="https://imgur.com/2ZVUz72.png" height="80%" width="80%" alt="SOC Project"/>
<img src="https://imgur.com/feck4Gg.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
I wanted to confirm that those components were successfully deployed and assigned to the HOMELAB-SOC-LAB resource group so I can proceed confidently without worrying that resources are in the wrong group.
<p align="center">
I have now checked and verified that all lab components were created. The next step is configuring firewall policies to make the virtual machine intentionally vulnerable and accessible from the Internet.<br/>
<img src="https://imgur.com/dA7bJFU.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
To turn the virtual machine into a honeypot, I configured the inbound security rules. This new rule allows the virtual machine to receive all inbound traffic, including connections from the Internet. To accomplish this, I deleted the existing 300 RDP inbound security rule and created a new one called Dangerous_AllowInboundRule, which permits any type of connection.
<br/>
<img src="https://imgur.com/qEIeZbs.png" height="80%" width="80%" alt="SOC Project"/>
<img src="https://imgur.com/gL3KoFM.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
In addition to adding the new inbound rule, I configured the Windows Firewall inside the VM. I connected via Remote Desktop using the VM credentials and used the Windows Defender Firewall with Advanced Security tool to disable all critical firewall functions across the Domain, Private, and Public profiles.
<p align="center">
I did not encounter any issues during this step, but I gained a better understanding of Windows Defender Firewall and the role of each profile. If an attacker can log into a system and obtain an administrator account to disable firewall protections, it could be disastrous for any organization.<br/>
<img src="https://imgur.com/iftpatO.png" height="80%" width="80%" alt="SOC Project"/>
<img src="https://imgur.com/KoVdPrW.png" height="80%" width="80%" alt="SOC Project"/>
<img src="https://imgur.com/J58x0h5.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
To verify that the firewall and inbound security rule configurations were completed correctly, I used a PowerShell command on my personal computer (PC) to ping the virtual machine. This ping test confirms whether my PC can send packets to the VM without any issues.<br/>
<img src="https://imgur.com/EZNxhEt.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
I used the Event Viewer tool in my VM to identify whether any login attempts were successful or failed. Before logging into the VM, I intentionally used incorrect credentials to generate a failed login event for review. In Event Viewer, I was able to see the event ID associated with failed login attempts, along with details such as the username used, the IP address from which the attempt originated, and the workstation name.
<p align="center">
I did not encounter any issues locating the failed login attempt I made. My main takeaway from this part of the project was learning how to filter events by their event ID and analyze the data captured during the event.
<br/>
<img src="https://imgur.com/zL9L8MQ.png" height="80%" width="80%" alt="SOC Project"/>
<img src="https://imgur.com/KTEobjw.png" height="80%" width="80%" alt="SOC Project"/>
<img src="https://imgur.com/7vi90n3.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
To process these events in Microsoft Sentinel, a log repository must be created to collect and manage the events occurring in the VM. In Azure, I used the Log Analytics workspace for this purpose. This resource is connected to the VM to collect and process event logs before forwarding them to the SIEM. The Log Analytics workspace was created under the HOMELAB-SOC-LAB resource group and named LOGANALYTICS-SOC-LAB.
<br/>
<img src="https://imgur.com/k1wkhxH.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
Now I can add Microsoft Sentinel to the log repository that was created, linking the SIEM and the Log Analytics workspace to work together.
<br/>
<img src="https://imgur.com/0jOwIgR.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
In Microsoft Sentinel, I will install Windows Security Events to the SIEM. This enables the Log Analytics workspace and the VM to connect simultaneously, allowing all events logged in the VM’s Event Viewer to be forwarded to the Log Analytics workspace. Consequently, the SIEM can collect and process these events.
<br/>
<img src="https://imgur.com/ZvWYgiA.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
Once Windows Security Events is installed, I will create a data collection rule through the Azure Monitor Agent (AMA) within Windows Security Events. This rule allows the VM to send event logs to the Log Analytics workspace.
<br/>
<img src="https://imgur.com/AsL4bh9.png" height="80%" width="80%" alt="SOC Project"/>
<img src="https://imgur.com/vkWxCeI.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
Now that all configurations and installations have been completed to sync the Log Analytics Workspace, VM, and SIEM, I can access the event logs through the Log Analytics Workspace. I switched to KQL mode, which allows me to create queries to display specific values.
<p align="center">
The query I ran displayed the time of occurrence, account, computer targeted by attackers, event ID, activity, and the IP address of the source attempting to log in.
<p align="center">
I did not encounter any complications during this part of the project. I gained a basic understanding of KQL and how to create queries. I also noticed similarities between KQL and SQL, which made learning KQL fundamentals easier.
<br/>
<img src="https://imgur.com/ZEu3M4C.png" height="80%" width="80%" alt="SOC Project"/>
<img src="https://imgur.com/bjmmgFl.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
While the information in the event logs is useful, creating a Watchlist in the SIEM allows for more in-depth analysis. This enables the display of an attack map showing where login attempts originate. To accomplish this, I will upload a document that includes the IP addresses along with their corresponding latitude, longitude, city name, and country name.
<br/>
<img src="https://imgur.com/z9RkuBg.png" height="80%" width="80%" alt="SOC Project"/>
<img src="https://imgur.com/GRe5Zyg.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
Within the SIEM, I added a workbook that includes a query displaying a geographic map of attacks targeting my VM. This query incorporates the watchlist and the event logs collected and processed by the Log Analytics workspace.
<br/>
<img src="https://imgur.com/mx8I85S.png" height="80%" width="80%" alt="SOC Project"/>
<br />
<br />
Overall, this SOC project has allowed to me understand how a SIEM works and its different functionalities, critical security features, security protocols, cybersecurity strategies, and problem solving when encountering a difficult problem.  
<br/>
<br />






<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
