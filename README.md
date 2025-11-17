# SOC-lab-detection-Splunk
A hands-on SOC lab using my homelab using Splunk Enterprise to simulate, detect and analyze attacks in a homelab environment. Includes Sysmon and Windows logs, SPL queries and incident reponse.

This repo is a hands-on SOC lab i built using Splunk Enterprise (Free trial) on my homelab environment. I did this mostly as preparation for taking the BTL1 Certification. This project focuses on collecting and analyzing windows securty logs and system events to detect threats and built blue team skills. The key goal is getting to know SIEM infrastructure to prepare for a SOC/Blue team role in the near future.

- Project Objectives
Deploy and configuring Splunk Enterprise as a central server.
Setting up Splunk Universal Forwaders to collect the Sysmon data and windows event logs.
build detection queries SPL
Create dashboards and analytics for SOC monitoring
Document incident response workflows.
Develop skills for blue team/SOC analyst roles.


Architecture:
[Architectuur .drawio](https://github.com/user-attachments/files/23582630/Architectuur.drawio)

Prerequisites:
Hardware
- Minimum 8GB RAM
- 100GB disk space
- Network connectivity

Software
- Splunk enterprise
- Splunk universal forwarder
- Sysmon
- Windows 10/11
- Ubuntu or another linux distro

Networking
- All machine on same LAN or routable trough a VPN
- port 8000 open on the central server
- port 9997 open on central server
- optional port 22 open for SSH
- firewall rules configured to allow bidirectional traffic

INSTALLATION & SETUP
Make a SPLUNK free trial account.
Go to downloads and use the WGET given by Splunk and download splunk enterprise on the server
Follow the instruction of downloading everything.

Acces the splunk web:
https://<server-ip>:8000

In the Splunk Web:
Go to Settings -> Forwarding and recieving -> configure receiving -> add new
Set listening port 9997
Save and restart Splunk service on linux machine
"sudo systemctl restart splunk"

Step 2 Install and Configure Sysmon on Windows Client
Download Sysmon and put it in a easy to find folder ( I called mine just "Sysmon" )
https://learn.microsoft.com/nl-nl/sysinternals/downloads/sysmon

Get a sysmon config
U can use a baseline or community config. Name it sysmon-config.xml and put it in the made folder
This is a example file:

<Sysmon schemaversion="4.50">
  <EventFiltering>
    <ProcessCreate onmatch="include"/>
    <NetworkConnect onmatch="include"/>
    <FileCreateTime onmatch="include"/>
    <ProcessTerminate onmatch="include"/>
    <ImageLoaded onmatch="include"/>
  </EventFiltering>
</Sysmon>

Install Sysmon (As a administrator)
Open Powershell as administrator:
cd C:\Tools\Sysmon\
.\sysmon64.exe -accepteula -i .\sysmon-config.xml

Verify installation:
Get-Service sysmon64
Expected output: Status should be "Running".


Step 3: install and configure Splunk Universal Forwarder on the windows

Download splunk universal forwarder from the same splunk page.

https://www.splunk.com/en_us/download/universal-forwarder.html

Choose the MSI installer
Choose the typical installation 
Set admin password during setup'

configure inputs.conf
Go to the following path in file explorer C:\Program Files\SplunkUniversalForwarder\etc\system\local\
Create a file named inputs.conf and safe it in the path.

[WinEventLog://Security]
disabled = 0

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0

[WinEventLog://System]
disabled = 0

[WinEventLog://Application]
disabled = 0

Do the same for a outputs.conf

[tcpout]
defaultGroup = splunk_indexers

[tcpout:splunk_indexers]
server = 192.168.1.xxx:9997

Run in powershell administrator
"Restart-Service SplunkForwarder"

Verify its running:
"Get-Service SplunkForwarder"

Search for incoming data
index=*











  
