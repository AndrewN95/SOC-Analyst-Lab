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

Now that the sliver server is up, I generated a payload using the command below.

![image](https://github.com/user-attachments/assets/1e348849-956d-4cf3-8758-6f8f135fc820)

To get the payload onto the Windows machine, I created a web server on the Kali machine and then typed the command from the guide which downloads the payload onto the Windows machine.

![image](https://github.com/user-attachments/assets/30398701-c456-4d7d-8543-8c2fe14e3458)
![image](https://github.com/user-attachments/assets/b7be15a6-ff84-4627-96e0-51889e07323a)

After that, I started the sliver listener by typing http and then I executed the payload on the Windows machine.

![image](https://github.com/user-attachments/assets/faebc068-cbb4-4128-84e6-9cbd21a35e1e)

# We're in. 
I typed 'sessions' which gave me the ID of the session and by typing 'use a60ebeea' I started a command and control session.

![image](https://github.com/user-attachments/assets/59cec898-0e64-4c08-b143-b048df56570b)

With ps -T we can find what security products are on the machine and our C2 session process.

![image](https://github.com/user-attachments/assets/598cca44-c946-44db-a858-10e63c5b4aea)

# Exploring LimaCharlie
Heading back to LimaCharlie, I explored the platform to gain a better understanding of its capabilities. I am able to see processes, and in particular, the process of the C2 session. I also went through timeline and file system which allowed to be check the hash of the file for OSINT.

![image](https://github.com/user-attachments/assets/8b2f8db5-2ee1-4f1d-8b3a-8aa8692d9d95)

# LSASS and detection
Now it's time to simulate an attack. With the sliver listener active, I run the malware on the Windows machine. From the Kali attack machine, I am able to type getprivs to see what privileges I have. The image below shows the SeDebugPrivilege which I can use procdump and lsass.exe to dump the remote process from memory which is a way to obtain operating system credentials for lateral movement.

![image](https://github.com/user-attachments/assets/b8e9adc3-cfc2-47bb-ae1b-a1e761a53c8d)

Back in LimaCharlie, I searched for the LSASS.exe process by looking for SENSITIVE_PROCESS_ACCESS. Once I found the process, I can click on the button shown below to create a detection rule.

![image](https://github.com/user-attachments/assets/8c5c42f3-0242-4785-8cfa-6ecef49669e0)

In the detection rule editor, I specified that the detection rule should look for SENSITIVE_PROCESS_ACCESS events that end with lsass.exe and the response should be to report it as a LSASS access.

![image](https://github.com/user-attachments/assets/2c1ad833-80be-43b3-bef2-82e5bc41ee82)

