# Active Directory Lab

## Objective

This lab is all about getting hands-on with Active Directory and security monitoring. I've set up Windows Server 2022 AD domain controller and a Ubuntu Splunk server, then used Kali Linux to play the role of an attacker in the network. The goal is to see what kind of events get generated from these attacks and learn how to configure and use Splunk to catch them. I aim to dive into domain environments and sharpen threat detection skills.

I followed <a href="https://www.youtube.com/watch?v=MHsI8hJmggI&t=3235s">Josh Madakor's video</a> and <a href="https://www.youtube.com/watch?v=mWqYyl89QaY">MyDFIR's video series</a> on YouTube as resources for setting up this lab.

This is by no means a comprehensive step-by-step of my process, but rather a few key processes that are interesting or will be useful for my future reference.
### Skills Learned

- Configuring and managing Active Directory domain environments
- Setting up and utilizing Splunk for security event monitoring and analysis
- Simulating bruteforce attack using Kali Linux
- Setting up Atomic Red Team and running tests
- Identifying and interpreting security events generated from attacks via Splunk
- Troubleshooting and fine-tuning Splunk data ingestion
- Understanding the interaction between different components in a domain environment

### Tools Used

- Windows Server 2022 (for Active Directory domain controller)
- Ubuntu Server (for Splunk server)
- Splunk (for security event monitoring and analysis)
- Kali Linux (for simulating bruteforce attack)
- Windows 10 Client (for interacting with the domain environment)
- Atomic Red Team (for simulating various attacks mapped to the MITRE ATT&CK framework)
- VirtualBox (for virtualising the lab environment)

## Network Diagram
![NetworkDiagram-ActiveDirectoryProject drawio](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/f44ce545-f677-4006-8a65-b447b6d2978f)

## Setting up Splunk server and Forwarders
### Setting Static IP Address and Default Route:

- Configured a static IP address for the Splunk server and defined a default route with the gateway 192.168.10.10.
```sudo nano /etc/netplan/00-installer-config.yaml```

![2024-06-28 18_49_35-Splunk  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/99ee31d7-40c0-4739-8581-c6d72613b066)

Apply the changes:
```sudo netplan apply```

### Install Splunk Enterprise:

- Installed Splunk Enterprise on the Splunk server and configure it to start at boot.

```mydfir@splunk:/opt/splunk/bin$ sudo ./splunk enable boot-start -user splunk```

### Setting Up Splunk Forwarder:
Installed and configured Splunk Forwarder on ADDC01 and target-PC to send data to the Splunk server as a receiving indexer.

![2024-06-28 22_10_10-ADDC01  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/54bdd7fe-3954-4c2e-8613-6ec049293241)

### Installing Sysmon:

Installed Sysmon to enhance event logging capabilities.

![2024-06-28 22_16_09-ADDC01  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/306160cf-69bd-4322-b2ee-50dbd73b66eb)

### Configuring Inputs for Splunk Forwarder:

Created an inputs.conf file in C:\Program Files\SplunkUniversalForwarder\etc\system\local on ADDC01 and target-PC, configuring settings as per MyDFIR's video.

![2024-06-28 22_18_36-ADDC01  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/7b973c10-11a6-4620-949e-bc233cf55736)
![2024-06-28 22_19_42-ADDC01  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/dc816365-9ca2-4435-af25-8d3e7e35aece)

### Restarting Splunk Forwarder Service:

Restarted the Splunk Forwarder service on ADDC01 and set to log on as local system account.
![2024-06-28 22_21_07-ADDC01  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/4aa87cdf-4e3e-49a8-a2fe-a0eae7de8f4f)

### Connecting to Splunk Web Interface:

Accessed the Splunk server's web interface at port 8000, then created an index named endpoint as specified in the inputs.conf file. I repeated this process for both ADDC01 and target-PC to ensure the Splunk server receives events from both sources.
![2024-06-29 23_05_02-target-PC (Snapshot 1)  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/fa26209e-7ad2-4136-a5f5-7ec7fba9c232)

## Setting up Active Directory and provisioning users.

Install Active Directory Domain Services on ADDC01

![2024-06-28 22_40_04-ADDC01  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/80c01684-787a-4639-8f61-a3a8ab7270a7)

Promote ADDC01 to Domain Controller

![2024-06-28 22_43_09-ADDC01  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/39653cdf-11f8-4527-ac06-588629e50e05)

I joined target_PC to the domain and tinkered around with users, groups and permissions. I used this script from Josh Madakor's video to create around 1000 users.

![2024-06-29 21_19_12-ADDC01 (Snapshot 1)  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/64f8e66c-9404-40de-bb38-5e15b0b4d69a)
![2024-06-29 21_23_00-ADDC01 (Snapshot 1)  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/5501aaa0-120e-4540-8630-00087fb0f77d)

## Performing a Brute force attack on target_PC and reviewing events via Splunk

I used crowbar to launch a brute force dictionary attack on target_PC from the Kali Linux machine. I had enabled RDP on target_PC beforehand so this attack would be feasable.

![2024-06-29 22_33_32-kali-linux-2024 1-virtualbox-amd64  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/bc6e4d08-5653-45e0-a549-00d88246d945)

After running the attack, we can see that Splunk recorded 42 events with event code 4265, which indicates failed login attempts. This corresponds to the 22 passwords in the wordlist I used for the attack, which was run twice.

Among these, there are two events with event code 4264, representing successful login attempts. This outcome is expected since one of the passwords in the wordlist was of course correct.

![2024-06-29 22_44_19-target-PC (Snapshot 1)  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/2d796524-390b-449e-86cc-615b9cad0b3a)
![2024-06-29 22_48_29-target-PC (Snapshot 1)  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/643611dd-b8e9-41d4-9784-60ffa72060ae)

Here we can see that the attack indeed came from the Kali machine at 192.168.10.250

![2024-06-29 22_47_20-target-PC (Snapshot 1)  Running  - Oracle VM VirtualBox](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/2770fcbd-b06a-4ba2-8b40-10d6a2ca347f)
