# TryHackMe: Fowsniff CTF

## Overview

This writeup documents my methodology for completing the Fowsniff CTF room on TryHackMe.

- **Platform:** TryHackMe
- **Difficulty:** Easy
- **Room:** Fowsniff
- **Tools used:** Nmap

## Reconnaissance and Scanning
Deployed the machine and I began with an Nmap service and default-script scan to identify the target's exposed services.

bash command
nmap -sC -A 10.82.166.8 -oN fowSniff.txt

Open Ports (refer to: 01-nmap-scan)
Port	Service	   Version
22	  SSH	       OpenSSH 7.2p2
80	  HTTP	     Apache 2.4.18
110	  POP3	     Dovecot
143	  IMAP	     Dovecot

## Web Enumeration
1) Based on inspection a web server is running on port 80 (refer to: 02-webserver
2) Fowsniff's internal system suffered a data breach that resulted in the exposure of employee usernames and passwords.
https://github.com/berzerk0/Fowsniff/blob/main/fowsniff.txt

## Credential Analysis
1)We copied the contents of leaked database into a .txt file named fowsniff_leaked.txt
command: nano fowsniff_leaked.txt

2) Used the command "sed -n 's/.*://p' fowsniff_leaked.txt > hashes.txt" to keep only the hashes.

The passwords are MD5 hashes, by using "https://hashes.com/en/decrypt/hash" we decoded/cracked 8 out of 9 hashed passwords
0e9588cb62f4b6f27e33d449e2ba0b3b:carp4ever
19f5af754c31f1e2651edde9250d69bb:skyler22
1dc352435fecca338acfd4be10984009:apples01
4d6e42f56e127803285a0a7649b5ab11:orlando12
8a28a94a588a95b80163709ab4313aa4:mailcall
90dc16d47114aa13671c697fd506cf26:scoobydoo2
ae1644dac5b77c0cf51e0d26ad6d7e56:bilbo101
f7fd98d380735e859f8b2ffbbede5a7e:07011972

3)Proceeded by creating two files one for the users and one for passwords

To create a users file we have used the command (Refer to: 

To create a password file we have used the command (Refer to: 

## POP3 Enumeration
1) Following room instructions we launched Metasploit from terminal by using the command "msfconsole"
2) As we saw before a pop3 is open, hence we have searched for pop3 exploit available by searching "search pop3"
3) We saw the exploit "auxiliary/scanner/pop3/pop3_login" as guided by the room instructions, hence we proceeded by selecting it by using the command "USE 3"
4) By running the command we identified a successful login
10.82.166.8:110       - 10.82.166.8:110 - Success: 'seina:scoobydoo2' '+OK Logged in.  '

## Initial Access
1) By using the command nc fowsniff10.82.166.8 110 we connected to pop3 by using credentials
> USER seina
> PASS scoobydoo2
> LIST -> Retrieve messages
> RETR 1 OR 2 to read messages

By reading message 1 we found an ssh password temp "S1ck3nBluff+secureshell". 
To identify the user that correlated to the specific password we have used hydra tool by running the command below:
hydra -L  users.txt -p S1ck3nBluff+secureshell ssh://10.82.166.8

User Identified: baksteen
pass: S1ck3nBluff+secureshell

## Privilege Escalation
by logging into ssh we then:
1) Proceeded to check which groups the user belongs too
2) Identified a file that can run by them by running the command find /  -group users -type f 2>/dev/null
3) We navigate to the specific folder to run the command



## Findings and Remediation

## Lessons Learned

## References
