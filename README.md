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

Image 1. Wazuh Dashboard Login

Once the Wazuh Dashboard was up and running, the next thing was to enable archives. By doing this, it would tell the server to store all logs even if the event did not trigger an alert. The main Wazuh configuration file was found to be the Ossec.conf file located in the var/ossec/etc directory. Root privileges were also needed to access this file.

<img width="825" height="60" alt="image" src="https://github.com/user-attachments/assets/7fc21320-2ad9-41f7-879f-47800dd09767" />

<img width="517" height="212" alt="image" src="https://github.com/user-attachments/assets/7c4b220f-e8f5-4810-bb7d-d84c7216d882" />










