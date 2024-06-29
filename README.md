# Active Directory Lab

## Objective

This lab is all about getting hands-on with Active Directory and security monitoring. I've set up Windows Server 2022 AD domain controller and a Ubuntu Splunk server, then used Kali Linux to play the role of an attacker in the network. The goal is to see what kind of events get generated from these attacks and learn how to configure and use Splunk to catch them. I aim to dive into domain environments and sharpen threat detection skills.

I followed <a href="https://www.youtube.com/watch?v=MHsI8hJmggI&t=3235s">Josh Madakor's video</a> and <a href="https://www.youtube.com/watch?v=mWqYyl89QaY">MyDFIR's video series</a> on YouTube as resources for setting up this lab.

This is by no means a comprehensive step-by-step of my process, but rather a few key processes that are interesting or will be useful for my future reference.
### Skills Learned

- Configuring and managing Active Directory domain environments
- Setting up and utilizing Splunk for security event monitoring and analysis
- Simulating network attacks using Kali Linux
- Identifying and interpreting security events generated from attacks
- Troubleshooting and fine-tuning Splunk data ingestion
- Understanding the interaction between different components in a domain environment

### Tools Used

- Windows Server 2022 (for Active Directory domain controller)
- Ubuntu Server (for Splunk server)
- Splunk (for security event monitoring and analysis)
- Kali Linux (for simulating network attacks)
- Windows 10 Client (for interacting with the domain environment)
- Various Kali Linux tools (e.g., Nmap, Metasploit, Wireshark)
- VirtualBox (for virtualising the lab environment)

## Network Diagram
![NetworkDiagram-ActiveDirectoryProject drawio](https://github.com/PaulMiguelSec/Active-Directory-Lab/assets/174075754/f44ce545-f677-4006-8a65-b447b6d2978f)
