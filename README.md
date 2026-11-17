# Wazuh-SIEM-Implementation-and-Analysis Lab

# Objective
To deploy and configure a Wazuh SIEM homelab using VMware to demonstrate centralized security monitoring, log ingestion, and threat detection across Windows and Ubuntu endpoints. This project allowed me to implement File Integrity Monitoring (FIM), and configuring Active Response capabilities to simulate real-world SOC operations and strengthen hands-on security skills.

# Lab Environment
VMWare Workstation
Wazuh Server (Ubuntu)
Ubuntu Endpoint
Windows Endpoint
Wazuh Agents

# 1. Wazuh Server Deployment
First part of this lab was installing the Ubuntu Server within VMWare to be utilized as the centralized Wazuh Server. Once the server was installed, I got the Wazuh installation commands directly from their website. Once that was installed, I had the User and Password needed to access the Wazuh dashboard.

<img width="400" height="250" alt="image" src="https://github.com/user-attachments/assets/be502bfa-a180-4646-b1d0-e8a96e096bbd" />

Image 1 Wazuh Dashboard Login

Once the Wazuh Dashboard was up and running, the next thing was to enable archives. By doing this, it would tell the server to store all logs even if the event did not trigger an alert. The main Wazuh configuration file was found to be the Ossec.conf file located in the var/ossec/etc directory. Root privileges were also needed to access this file. As shown below once inside the file, the logall and logall_json by default were set to no and needed to updated to yes. I then learned that every time changes are made to the ossec.conf file, the Wazuh manager needs restarted. This was accomplished with command SYSTEMCTL RESTART WAZUH-MANAGER.SERVICE.

<img width="825" height="60" alt="image" src="https://github.com/user-attachments/assets/7fc21320-2ad9-41f7-879f-47800dd09767" />
<img width="517" height="212" alt="image" src="https://github.com/user-attachments/assets/7c4b220f-e8f5-4810-bb7d-d84c7216d882" />

Images 2 & 3 Ossec.conf Directory and Configurations

Next part in enabling archives was to update the Wazuh filebeat configuration which essentially is used to securely send alerts from the Wazuh server to the indexer. Located in the etc/filebeat directory, the file accessed was filebeat.yml. The image below shows under filebeat modules and archives, by default was set to false and needed updates to true. The filebeat service also needed to be restarted with command SYSTEMCTL RESTART FILEBEAT.SERVICE.

<img width="450" height="300" alt="image" src="https://github.com/user-attachments/assets/0b514ee3-f820-459d-b9d3-06a569103187" />

Image 4 Filebeat Configuration File

Once this was completed, a new index pattern was created inside the Wazuh dashboard to showcase where the alerts generated throughout this project will be stored.

<img width="700" height="289" alt="image" src="https://github.com/user-attachments/assets/9b75598d-672d-4c04-9bc7-63aef118377d" />

Image 5 Archives Index Pattern

# 2. Connecting Agents and Sysmon installation
Another Ubuntu server and Windows 10 virtual machine were installed as the endpoints and then needed to be deployed as new agents inside the Wazuh dashboard. With open source information I was able to go step by step in deploying the windows and Ubuntu machines. The key steps in this process was making sure the Wazuh server IP address was assigned so that the agents could communicate with the server and running the commands to download/install the agents. I did also utilize SSH to access Ubuntu and RDP for the windows machine.
<img width="1382" height="64" alt="image" src="https://github.com/user-attachments/assets/84a6deff-d26e-4ba8-ba2e-57263f659f55" />
<img width="1313" height="86" alt="image" src="https://github.com/user-attachments/assets/329c9d5c-35ca-4558-b9e8-b5d6daa5ccd6" />
<img width="2090" height="130" alt="image" src="https://github.com/user-attachments/assets/64fc6a3f-2821-4705-8072-f21c2de696b3" />
Images 6, 7, & 8 Download/Install Commands and Connected Agents

I needed to next figure out how to get Sysmon logging active. Utilizing open source material and videos, I went through the steps, first going to the official Sysmon website to be downloaded. A Sysmon configuration file was also needed. I did utilize a walkthrough tutorial for these steps making sure I could successfully get Sysmon up and running. First agent I did this on was the Windows machine and made sure to install it with the correct config file. During this process I learned that settings in Wazuh needed to be configured to push Sysmon data log activity. Inside the Windows machine I had to locate the ossec.conf file to see these configurations, opened in Notepad as administrator needing those level of privileges. I discovered that the main contents of the file showed event logs that are looked at inside Event Viewer, the local files pointed to the main windows event logs of application, security, and system. This config file needed to altered so that it points to Sysmon logs. Switching from the Windows machine to Ubuntu, a Sysmon configuration file was also installed and confirmed that these logs are located in the /var/log directory and captured inside syslog.

<img width="470" height="185" alt="image" src="https://github.com/user-attachments/assets/da403764-880d-49e5-a1f5-762d07dee46d" />
<img width="502" height="27" alt="image" src="https://github.com/user-attachments/assets/0b1f8c8f-3035-47a2-b1b6-df00ef8a04a6" />
















