# TryHackMe - Vulnversity Penetration Test Report

**Date:**  10-08-2025 
**Tester:** Nakulan  
**Target IP:** `10.201.27.140`  
**Room Link:** [Vulnversity](https://tryhackme.com/room/vulnversity)  

---

## 1. Executive Summary
This penetration test was conducted against the Vulnversity target to identify and exploit vulnerabilities in a simulated environment.  
Key findings included:
- Unpatched services exposing unnecessary ports
- Misconfigured web application parameters
- Insecure file permissions leading to privilege escalation

---

## 2. Scope
**In Scope:**
- Host: `10.201.27.140`
- Services running on open ports
- Web application hosted on HTTP

**Out of Scope:**
- Attacks on other IPs
- Denial of Service (DoS)

---

## 3. Methodology
The engagement followed a standard pentesting approach:
1. **Reconnaissance** (service and directory enumeration)
2. **Exploitation** (web parameter tampering)
3. **Privilege Escalation** (SUID abuse)
4. **Post-Exploitation** (flag retrieval)

---

## 4. Findings

### 4.1 Reconnaissance
**Tool:** Nmap  
```bash
nmap -sV 10.201.27.140
```
**Result:**  
- Open ports: 21 (FTP), 22 (SSH), 80 (HTTP)  
- Services revealed potential entry points

---

### 4.2 Web Enumeration
**Tool:** Gobuster  
```bash
gobuster dir -u http://10.201.27.140 -w /usr/share/wordlists/directory-list-1.0.txt
```
**Result:**  
- `/internal` directory discovered

---

### 4.3 Exploitation
**Tool:** Burp Suite  
- Intercepted HTTP traffic to identify hidden form parameters  
- Modified POST request to bypass authentication  

---

### 4.4 Privilege Escalation
**Tool:** find command  
```bash
find / -perm -4000 -type f 2>/dev/null
```
- Found SUID binary allowing root execution  
- Exploited to spawn root shell

---

## 5. Proof of Exploitation
**Root Flag:**  
```
a58ff8579f0a9270368d33a9966c7fd5
```

---

## 6. Recommendations
- Remove unused SUID binaries
- Sanitize and validate all user input in web applications
- Restrict directory access on the web server
- Regularly patch services

---

## 7. Lessons Learned
- Thorough enumeration is key to uncovering hidden attack surfaces  
- Misconfigured permissions can quickly lead to full system compromise  
- Web proxy tools like Burp Suite are essential for identifying insecure parameters

