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

I needed to next figure out how to get Sysmon logging active. Utilizing open source material and videos, I went through the steps, first going to the official Sysmon website to be downloaded. A Sysmon configuration file was also needed. I did utilize a walkthrough tutorial for these steps making sure I could successfully get Sysmon up and running. First agent I did this on was the Windows machine and made sure to install it with the correct config file. During this process I learned that settings in Wazuh needed to be configured to push Sysmon data log activity. Inside the Windows machine I had to locate the ossec.conf file to see these configurations, opened in Notepad as administrator needing those level of privileges. I discovered that the main contents of the file showed event logs that are looked at inside Event Viewer, the local files pointed to the main windows event logs of application, security, and system. This config file needed to be altered so that it points to Sysmon logs. Switching from the Windows machine to Ubuntu, a Sysmon configuration file was also installed and confirmed that these logs are located in the /var/log directory and captured inside syslog. To show a basic proof of concept, I ran the whoami command from the terminal to confirm this would be shown in the dashboard. This is all shown in the images below.

<img width="470" height="185" alt="image" src="https://github.com/user-attachments/assets/da403764-880d-49e5-a1f5-762d07dee46d" />
<img width="502" height="27" alt="image" src="https://github.com/user-attachments/assets/0b1f8c8f-3035-47a2-b1b6-df00ef8a04a6" />
<img width="1865" height="142" alt="image" src="https://github.com/user-attachments/assets/ded824c9-4f51-488a-a8f3-1c7bfdeb9e8a" />

Images 9, 10, & 11 Sysmon installation and generated log data

# 3. Generating Telemetry
The Next part of this project was to create and understand the telemetry generated from different logs. Windows endpoint telemetry and analysis can show many valuable things during an investigation through different security event logs and Sysmon logs. I wanted to first demonstrate activity from enabling guest accounts and user creation. In the windows machine, I utilized the net user command to trigger events where the guest account was enabled, the password was updated and a new user was created. I went back into Wazuh and refreshed recent activity to find the generated logs. Reviewing the log activity, I filtered for the windows agent and 'NewUser' which helped in shortening the recent logs. A crucial piece of information I reviewed were the windows event IDs. The first one displayed as (data.win.system.eventID 4720) would indicate a new user was created, and (data.win.system.eventID 4722) indicated that a new user account was enabled. Taking it another step further I wanted to trigger activity from a user being added to a group. I ran the command net localgroup Administrators NewUser /add. From there I refreshed Wazuh log activity and found all the info of the process creation including the parent command line and command line. This documentation is shown in the images below.

<img width="555" height="254" alt="image" src="https://github.com/user-attachments/assets/6663ba72-66ed-4708-a289-8ee5757fdaad" />
<img width="820" height="285" alt="image" src="https://github.com/user-attachments/assets/56a2a591-5ba7-4c29-ae6c-7a86643b5a0d" />
<img width="590" height="382" alt="image" src="https://github.com/user-attachments/assets/ae0fbc41-80bc-46f5-b2d1-1c089b4c751a" />
<img width="545" height="51" alt="image" src="https://github.com/user-attachments/assets/3a2b7181-5b9e-4be4-bb7e-aaf36419d60d" />
<img width="493" height="417" alt="image" src="https://github.com/user-attachments/assets/a21c7041-f5cc-4bfc-ade6-0527cf0555b4" />

Images 12, 13, 14, 15, & 16 Windows Event Logs Showcasing User creation and Updated Privileges

Next I pivoted over to the Ubuntu machine to showcase SSH user access logs and activity. From my host PC command prompt I SSH into the ubuntu machine, from there was able to find the corresponding log in Wazuh and found it in the auth.log file inside the var/log directory. Now that I had successful login attempts, wanted to then switch and have multiple failed SSH login attempts quickly after one another. Reviewing activity again I could see this in the auth.log file as well as in Wazuh. Something I found interesting where in Wazuh it showed the MITRE attack IDs, tactic and techniques based on this type of behavior, indicating password guessing and a potential brute force related attack. This is all documented below.

<img width="712" height="59" alt="image" src="https://github.com/user-attachments/assets/aaf384bb-fdc1-49fc-b493-6cc33d742197" />
<img width="902" height="36" alt="image" src="https://github.com/user-attachments/assets/a74ee466-48ef-49d3-abe6-9a803be385ea" />
<img width="1028" height="176" alt="image" src="https://github.com/user-attachments/assets/8a80e982-725b-4d9c-99d7-0fd72d9d6ce9" />
<img width="495" height="107" alt="image" src="https://github.com/user-attachments/assets/dc70b1d7-23ce-4ae7-9933-de83e5800a4a" />

Images 17, 18, 19, 20
Linux SSH Logs of Successful and Failed Login Attempts

# File Integrity Monitoring
The next main part of this lab I wanted to focus on was learning and demonstrating how File Integrity Monitoring (FIM) works within a SIEM environment. This is a process in cybersecurity that tracks, analyzes and validates any critical files within an organization that may have been tampered with our corrupted in some way. This is very valuable with the amount of important files every organization must keep secure. Also from reviewing these settings it showed some default directories that would be getting looked at in real time indicating if anything was changed, FIM would start working to see what happened. I looked at this in Notepad via Administrator privileges inside the windows machine, which is where I also created a new file and directory to be analyzed. A new directory was created called 'Classified' and inside was a created text file called 'SensitiveData'. FIM settings could be found within the main Wazuh config file Ossec.conf, inside there were some specific FIM settings to take note of. It showed the frequency that syscheck is executed being every 12 hours, default files to be monitored and the real time monitoring. So that FIM would also start monitoring in real time for the new directory that I just created, I needed to added this entry in the settings. This is shown in the image below.

<img width="260" height="80" alt="image" src="https://github.com/user-attachments/assets/0dbf5933-a1d6-4c74-9ef3-05c3d0631989" />
<img width="800" height="350" alt="image" src="https://github.com/user-attachments/assets/7dda9e9f-6bba-45da-81fb-b61e6f082d66" />





















