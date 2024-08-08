# Cybersecurity Vulnerability Management Project using OpenVAS

![image](https://github.com/user-attachments/assets/c8c6c061-18aa-4d0a-9036-c0f514a8d084)

## Introduction

In this project, I Set up a secure Azure network with an OpenVAS Vulnerability Management Scanner VM.Developed a vulnerable Windows 10 VM, featuring outdated software and disabled security controls.Performed unauthenticated and credentialed vulnerability scans using OpenVAS.Analyzed scan results, highlighting the difference between unauthenticated and credentialed scans.Remediated identified vulnerabilities, verified successful remediation through subsequent scans.Created a list of remediable vulnerabilities to simulate realistic vulnerability remediation scenarios.

## Step by step implementation
## 1. Prepare Vulnerability Management Scanner

https://portal.azure.com → Go to the Marketplace → “OpenVAS secured and supported by HOSSTED”

“Start with a pre-set configuration”-->“Continue to Create VM”-->Resource Group: Vulnerability-Management-->VM Name:OpenVAS -->Authentication: Username → ***/ *** -->Create → Create

![3](https://github.com/user-attachments/assets/2443278e-9f5e-4cc9-aba7-93ab166be9b1)

After the VM has been created, SSH into it With PowerShell (windows) or Terminal (MacOS) using the credentials you created earlier.

<img width="1032" alt="1" src="https://github.com/user-attachments/assets/ac3b38c4-979b-409e-a51f-780b8cb76618">

It should show the web app URL and default username and password at this point, attempt to go to the URL in the browser and login with the username and password.


## Create Client Virtual Machine and Make it Vulnerable

https://portal.azure.com/
1. Virtual Machines and create a new Virtual Machine
2. Resource Group: Vulnerability-Management
3. VM Name: Win10-Vulnerable
4. Region: Same as the OpenVAS VM 
5. Virtual Network: Same as OpenVAS 
6. Image: Windows 10 Pro
7. Username: **** /***
8. Networking: Same Vnet as OpenVAS
9. Create the VM

After the VM has been created, ensure you can RDP into it with the credentials you created.
After logging in, make the VM vulnerable:
Disable the Windows Firewall and install few outdated softwares to make the system vulnerable.

<img width="799" alt="firewall" src="https://github.com/user-attachments/assets/670c0b78-52a6-4b3b-b2d1-6750668fa841">

<img width="455" alt="outdated soft1" src="https://github.com/user-attachments/assets/f0b1791d-513c-42d8-b6f3-0ef9c789621f">

<img width="381" alt="oudated software2" src="https://github.com/user-attachments/assets/48867deb-b1dc-4378-b962-186630d993b9">


Restart the VM

## Configure and OpenVAS to Perform First Unauthenticated Scan against our Vulnerable VM
Login to OpenVAS → Assets → Hosts → New Host
Add the Client VM PRIVATE IP Address
Create a New Target from the Host, name it “Azure Vulnerable VMs”.

<img width="470" alt="target" src="https://github.com/user-attachments/assets/5497c1d2-54db-4785-9775-cdbd5d7aa0e7">

Create a new Task
Name & Comment: “Scan - Azure Vulnerable VMs”
Scan Targets → “Azure Vulnerable VMs”
Save the Task

<img width="1916" alt="task created" src="https://github.com/user-attachments/assets/58be6381-5fc9-4897-8bb0-5ea81f51712c">

Start” the “Scan - Azure Vulnerable VMs” Task

<img width="1919" alt="scan started" src="https://github.com/user-attachments/assets/a6d872bd-78a9-49f8-9738-33200457dfa9">

Once the scan is finished, click the date under “Last Report” to see the results.

<img width="1850" alt="unauthenticated scan vulnerability" src="https://github.com/user-attachments/assets/2e2116c3-8d38-4816-b59b-abd3853071f8">

## Make Configurations for Credentialed Scans (Within VM)

1. Disable Windows Firewall
2. Disable User Account Control
3. Enable Remote Registry
4. Set Registry Key
Launch Registry Editor (regedit.exe) -->Navigate to HKEY_LOCAL_MACHINE hive-->Open SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System-->Create a new DWORD (32-bit) value with the following properties:  Name: LocalAccountTokenFilterPolicy  Value: 1

![useraccount](https://github.com/user-attachments/assets/11dedcbc-3cb6-445b-aab8-9f9b77660404)

<img width="1423" alt="registry editings" src="https://github.com/user-attachments/assets/bdc6aedb-594c-4e94-b824-ca329bcf9a41">

Restart the VM.

## Make Configurations for Credentialed Scans (OpenVAS)

Go to Configuration → Credentials → New Credential
Name / Comment → “Azure VM Credentials”

Allow Insecure Use: Yes
Username: ****
Password:****
Save

<img width="860" alt="new credential" src="https://github.com/user-attachments/assets/151e31ea-0eeb-4f89-ae09-c764db29703d">

Go to Configuration → Targets → CLONE the Target 
Credentials → SMB → Select the Credentials we just made: Azure VM Credentials

## Execute Credentialed Scan against our Vulnerable Windows VM

1. Greenbone / OpenVAS, go to Scans → Tasks
2. CLONE the “Scan - Azure Vulnerable VMs” Task, then Edit it:
3. Name / Comment → “Scan - Azure Vulnerable VMs - Credentialed”
4. Targets: Azure Vulnerable VMs - Credentialed Scan
5. Save
6. Click the Play button to launch the new Credentialed Scan, wait for it to finish

<img width="1652" alt="credential scan1" src="https://github.com/user-attachments/assets/1f1ef1ee-00f6-4402-a2a4-98dcd32b4f15">

After the credentialed scan finishes, you can immediately see the difference in findings:

<img width="1850" alt="credential scan reports" src="https://github.com/user-attachments/assets/67158c5b-1b37-4f6b-b3d7-6dc3f8ed4dc9">

## Remediate Vulnerabilities

1. Log back into your Win10-Vulnerable VM
2. Uninstall Adobe Reader, VLC Player, and Firefox

<img width="588" alt="uninstall1" src="https://github.com/user-attachments/assets/a0f3e338-0ce9-4b83-a0ba-258b2a70714c">


Restart the VM

## Verify Remediations

Re-initiate the “Scan - Azure Vulnerable VMs - Credentialed” scan and observe the results.

<img width="771" alt="credential scan 2" src="https://github.com/user-attachments/assets/09a97bef-b5c4-4b4b-9e0c-412e2d89da25">

No vulnerabilties were there in the reports after the remidiations were applied.
































