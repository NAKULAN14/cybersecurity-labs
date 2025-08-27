# TryHackMe - Simple CTF Penetration Test Report  

**Date:** 12-08-2025  
**Tester:** Nakulan  
**Target IP:**   10.201.57.141
**Room Link:** [Simple CTF](https://tryhackme.com/room/easyctf)  

---

## 1. Executive Summary  
This penetration test was performed on the **Simple CTF** room to identify and exploit vulnerabilities in a simulated environment.  

**Key Findings:**  
- Multiple services exposed on low and high ports  
- Web application vulnerable to **SQL injection (CVE-2019-9053)**  
- Weak credentials retrieved from the database allowed SSH login  
- Misconfigured privilege escalation via `vim` enabled root compromise  

---

## 2. Scope  
**In Scope:**  
- Host: ``  
- Services exposed on open ports  
- Web application hosted on HTTP  

**Out of Scope:**  
- Attacks on unrelated IPs  
- Denial of Service (DoS)  

---

## 3. Methodology  
The pentest followed these phases:  
1. **Reconnaissance** – Service & directory enumeration  
2. **Exploitation** – SQL injection, credential harvesting  
3. **Privilege Escalation** – Exploiting misconfigured binaries  
4. **Post-Exploitation** – User and root flag retrieval  

---

## 4. Findings  

### 4.1 Reconnaissance  
**Tool:** Nmap  
```bash
nmap -sV  10.201.57.141
```
**Result:**

-Services under port 1000: 2
-Higher port: SSH (22)

### 4.2 Web Exploitation
**Tool:** SQLMap


``` bash
sqlmap -u "http:// 10.201.57.141/search.php?id=1" --dbs 
```
**Found vulnerability:** SQL Injection (SQLi)

**CVE used:** CVE-2019-9053

**Extracted credentials:**

**Username:** <from db>

**Password:** secret

### 4.3 SSH Login
Used discovered credentials:

ssh <username>@< 10.201.57.141>
Successfully logged in with password secret.

### 4.4 User Flag
Located in user’s home directory:

G00d j0b, keep up!

### 4.5 Additional User Enumeration
Checked /home directory:

Found another user: sunbath

### 4.6 Privilege Escalation
Checked SUID binaries:

```bash

find / -perm -4000 -type f 2>/dev/null
```

Found misconfigured vim binary.

Escalated privileges to root using:

vim -c ':!/bin/sh'

4.7 Root Flag
Located in /root:


W3ll d0n3. You made it!

## 5. Proof of Exploitation
User flag: G00d j0b, keep up!

Root flag: W3ll d0n3. You made it!

## 6. Recommendations
Patch web application to fix SQL injection (CVE-2019-9053)

Enforce stronger SSH credentials

Remove or restrict dangerous SUID binaries (e.g., vim)

Monitor for suspicious user accounts and activity

## 7. Lessons Learned
Enumeration quickly reveals vulnerable services and ports

SQL injection remains one of the most impactful web vulnerabilities

Weak SSH credentials are a common entry point in CTFs and real systems

Privilege escalation often relies on overlooked SUID binaries