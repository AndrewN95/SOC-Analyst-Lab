# SOC-Analyst-Lab
This project follows Eric Capuano's SOC Analyst lab which covers virtualization, adversary simulation, fine tuning of detection rules, incident response, and rule-based threat detection. The lab makes use of LimaCharlie, a cloud-based SOC platform.

# Setting up the virtual environment
Instead of using VMware, I used my homelab hosted on Proxmox. After the installation of the Ubuntu server and Windows 10 machines, I logged onto the Windows VM first. The first step in the guide is to disable Microsoft Defender which included disabling tamper protection, disabling Microsoft Defender via Group Policy Editor, disabling defender via registry, and booting in safe mode to disable more services via registry. After that, I proceeded onto the next step of installing Sysmon. I ran the commands from the lab and installed sysmon using SwiftOnSecurity's config.

![image](https://github.com/user-attachments/assets/21089ac7-fbb6-49a9-b9c2-a1188c8d106b)

Following the installation of Sysmon, I created an account with LimaCharlie and established my new organization. With LimaCharlie, I added a sensor to my Windows vm which acts as an EDR.

![image](https://github.com/user-attachments/assets/57470284-c64a-40db-9f3f-648602c59722)

Next, I configured LimaCharlie to send both Sysmon event logs and its EDR telemetry.

![image](https://github.com/user-attachments/assets/03714637-ed10-4411-bde3-6a1d897c6d76)
![image](https://github.com/user-attachments/assets/0a8c22c2-50f2-489b-bafa-ecf3ab8e1f61)

Following Eric's tip, I took a snapshot of my Windows VM and then started with the deployment of the linux attack machine. First, I downloaded Sliver, a C2 framework by BishopFox, through the commands provided by the lab. I then created a directory for sliver.

![image](https://github.com/user-attachments/assets/22794c5e-a923-4597-bb66-88b5d9a66a17)

# Creating an adversary
Once I changed into the directory of sliver, I launched the sliver server.

![image](https://github.com/user-attachments/assets/4d7d8dbd-7685-44a6-8758-b08af014c74b)

Now that the sliver server is up, I generated a payload using the command below

![image](https://github.com/user-attachments/assets/1e348849-956d-4cf3-8758-6f8f135fc820)
