# OpenVAS
In this project, I set up a secure Azure network environment with an OpenVAS Vulnerability Management Scanner VM. I developed a purposely vulnerable Windows 10 VM by including outdated software and disabling security controls. Using OpenVAS, I conducted both unauthenticated and credentialed vulnerability scans, analyzing the results to highlight the distinctions between the two methods. After identifying vulnerabilities, I performed remediation and verified successful remediation through follow-up scans. To simulate realistic vulnerability remediation scenarios, I compiled a list of remediable vulnerabilities, ensuring a comprehensive approach to network security.

**Skills: Microsoft Azure · Vulnerability Management · Virtual Machines · System Administration · Network Security · Cryptography**

# Let's get started: 
1. Sign up for an [Azure account](https://azure.com/free). If you are new, you will be able to receive 200 dollars free credit from Microsoft.

2. Log in via [here](https://portal.azure.com). Create a Virtual Machine, you can do this by going to "Marketplace" and type 'OpenVAS".
 <img width="355" alt="image" src="https://github.com/user-attachments/assets/e4903dd1-1c61-4ec7-9d44-a416c593d990">

3. For the VM:
   - Resource Group: You can name this whatever you prefer.
   - VM name: OpenVAS
   - Region: Whatever you prefer, I went with East US 2
   - Image: Select basic plan -x64 Gen 1
   - Create a username and a password (write this info down somewhere to avoid forgetting)
   - write down the Virtual Network name under the "Networking" tab, this would be important for the second Windows VM
   - Disable boot diagnostic under the "Monitoring" tab
   - Now you created your VM!
  
  # SSH into Linux VM
  (for Macbook users, open terminal, for windows, open PowerShell via administrator)
  Run the following command: `ssh [username]@[linux VM IP]`
Type yes when prompted, and enter the password from step 3.

4. install OpenVas on the [Linux VM](https://www.geeksforgeeks.org/installing-openvas-on-kali-linux/). run these commands:


```bash
sudo apt update

sudo apt upgrade -y

sudo apt dist-upgrade -y

sudo apt install openvas

sudo gvm-setup

sudo gvm-check-setup
```
5. 
![image](https://github.com/user-attachments/assets/8d8b5b04-739c-406b-96e7-8c66dd534c2a)

When you reach this step, there will be a username and password (write it down), and there should also be a link to the web server.

6. Wait a few days before logging into the web server so we can ensure the site has been configured correctly, then we enter the username and password from step 5.

7. Check to make sure the Scan Configs are available or the lab won't work, check this after logging into the webserver ("configuration" tab) and select Scan Configs. This page cannot be empty, if it is, go over the SSH Linux VM setup part again.

# Make a vulnerable Windows VM
8. same steps, just make sure it is a Windows 10 Pro VM, put it as the same region as VM 1, make a username and password.
   - under "networking", pick the same virtual network so OpenVAS can scan the Windows 10 because they will both be in the same private network.

# RDP VMs
9. (Macbook users download "Microsoft Remote Desktop", Windows users search up "Remote Desktop Connection", and copy the Windows VM IP address (Azure Portal > Overview > Public Address)
10. Once you connect, select "change option" and put the username  from step 8.
11. Log in, and disable the FW. Search the Wf.msc command, click on "Windows Defender Firewall Properties" and disable domain, public and private firewalls.
![image](https://github.com/user-attachments/assets/a08717f0-fa20-45b9-b432-ae9e39576503)


12. Open this link on the [internet browser](https://drive.google.com/drive/u/0/folders/1n83ilCjZWZulbDdYnUe9wQPK2buY47_U), download and install the software, these are old versions of Firefox, VLC player and Adobe which means they are more vulnerable than the newer models.
13. Restart the VM and leave it alone.

# Configure and Perform Unauthenticated Scan on OpenVAS

14. Log into OpenVAS from an incognito tab
- We need to add the client Windows VM on OpenVAS so it can start scanning. Under "Assets" click "Hosts" then "New Host". Copy the Window VM's private IP (refer to step 9 if unsure), and save.

15. Create a target from the Host and name it (Configuration > Target > New Target), save and create a new task (Scans > Tasks > New Task), Name it: Scan -Azure Vulnerable VMs. Pick target: Azure Vulnerable VMs.


![image](https://github.com/user-attachments/assets/2b13ed7f-62c4-4b54-8de5-9ac5de34a4cc)

![image](https://github.com/user-attachments/assets/cc6c816a-3785-4211-818f-c9ac45c2a7f0)

16. This is what you should see and then click the play button to start scanning
![image](https://github.com/user-attachments/assets/1fdd18dd-4f0b-4c71-8de5-49b561bc2649)
![image](https://github.com/user-attachments/assets/39d97278-a033-428d-a06d-a78d88ed87a2)
- Once the status bar goes to 100%, click on the report link to analyze what you see.

17. Looking at our scan, we can see that our downloaded software is nowhere to be found, which is normal since our scan is unauthenticated, it won't be detailed and precise.

# Configure Windows VM for authenticated scans

18. Log into Windows VM > User Account Control > Never Notify then save.
19. Services.msc > Remote Registry > Change Startup Type to automatic > Apply > Start
![image](https://github.com/user-attachments/assets/d0376c85-fa5c-4538-8cc1-dbd4118584a4)

20. Go to Registry Editor, Navigate to HKEY_LOCAL_MACHINE hive → Open SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System key.

21. Create a new DWORD (32-bit) value with the following properties:

Name: LocalAccountTokenFilterPolicy

Value: 1

22. Restart the VM

# Configure OpenVAS for Authenticated Scan

23. Go to “Configuration” and “credential”. Create a new credential. Name it: Azure VM Credentials. Allow insecure use. Username and pw= same as what we have for our windows VM. Save it.
24. Now we clone the target. Rename our Cloned target to: Azure Vulnerable VMs — Credentialed Scan. For “credentials for authenticated checks”, go to SMB and select the Azure VM credentials we created.

25. Execute Credentialed Scan
![image](https://github.com/user-attachments/assets/56d14ac0-3112-44e0-810f-218dd2cb952e)

26. Run the scan again, after seeing the results, we can see that there are a lot more results for vulnerabilities found, our findings confirm OpenVAS was able to connect the Windows VM.
27. Delete all the old software we downloaded (Control Panel > Programs > Uninstall a program)
28. Run the scan again, we can see there are a lot fewer vulnerabilities found in comparison to the sight we've seen from the downloaded software.


## Congrats on making it this far, and thank you for following my journey on this vulnerability management lab.
