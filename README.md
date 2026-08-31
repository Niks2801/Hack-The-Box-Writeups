# Hack The Box — Grind & Writeups

<p align="center">
  <img src="https://img.shields.io/badge/Hack%20The%20Box-Writeups-9FEF00?style=for-the-badge&logo=hackthebox&logoColor=white" alt="Hack The Box">
  <img src="https://img.shields.io/badge/Cybersecurity-Offensive%20Security-111111?style=for-the-badge&logo=hackthebox&logoColor=9FEF00" alt="Cybersecurity">
  <img src="https://img.shields.io/badge/Status-In%20Progress-orange?style=for-the-badge" alt="Status">
</p>

<p align="center">
  <b>Learning offensive security through hands-on labs, one machine at a time.</b>
</p>

---

## About This Repository

This repository contains my **Hack The Box writeups, notes, commands, methodologies, and lessons learned** while working through HTB machines and challenges.

The goal isn't simply to collect flags.

> **The goal is to understand why the attack worked.**

Each writeup documents the process from **reconnaissance** and **enumeration** to **exploitation**, **privilege escalation**, and **flag retrieval**.

---

## What I'm Learning

- **Reconnaissance & Enumeration**
- **Web Application Security**
- **Linux Privilege Escalation**
- **Windows Privilege Escalation**
- **Authentication & Access Control**
- **Command Injection**
- **Server-Side Vulnerabilities**
- **Database Enumeration**
- **Network Enumeration**
- **Exploit Analysis**
- **Post-Exploitation**
- **Vulnerability Research**
- **Offensive Security Methodology**

---

## Repository Structure

```text
HTB-Writeups/
│
├── README.md
│
├── Starting Point/
│   ├── Command/
│   │   ├── README.md
│   │   └── screenshots/
│   │
│   └── ...
│
├── Machines/
│   ├── Linux/
│   ├── Windows/
│   └── ...
│
├── Challenges/
│   ├── Web/
│   ├── Crypto/
│   ├── Pwn/
│   ├── Reverse/
│   └── Forensics/
│
└── Notes/
    ├── Enumeration/
    ├── Privilege-Escalation/
    ├── Web/
    └── Cheatsheets/
Methodology

I try to follow a repeatable penetration-testing methodology instead of immediately searching for an exploit.

1. Reconnaissance

Understand the target and identify the attack surface.

Typical tools:

Nmap
RustScan
WhatWeb
Nikto

Questions to answer:

What ports are open?
What services are running?
What technologies are being used?
Is there a web application?
Are there interesting hostnames or domains?
2. Enumeration

Once services are identified, enumerate them thoroughly.

For web applications:

Directories
Endpoints
Parameters
Source Code
JavaScript
API Endpoints
Authentication
Cookies
Headers
Technology Stack

For Linux and Windows machines:

Users
Shares
Services
Configurations
Credentials
Scheduled Tasks
SUID / Capabilities
Interesting Files
3. Vulnerability Discovery

Connect the enumeration results to potential attack paths.

Examples:

Exposed Credentials
Misconfigured Services
Weak Authentication
Command Injection
SQL Injection
File Inclusion
File Upload Vulnerabilities
Information Disclosure
Privilege Escalation
4. Exploitation

Validate the vulnerability and obtain the required access.

The focus is not just:

"What payload works?"

but:

"Why does this payload work?"

Understanding the vulnerability makes it easier to recognize and exploit similar issues on future targets.

5. Initial Access

After obtaining access, enumerate the compromised environment.

Identify Current User
        |
        v
Enumerate Environment
        |
        v
Find Credentials / Secrets
        |
        v
Enumerate Local Services
        |
        v
Search for Privilege Escalation
6. Privilege Escalation

Privilege escalation depends on the target operating system.

Linux
sudo -l
find / -perm -4000 2>/dev/null
getcap -r / 2>/dev/null
Windows
whoami /all
systeminfo
net user
net localgroup administrators

The exact techniques and commands will be documented within each individual writeup.

Common Tools
Area	Tools
Reconnaissance	Nmap, RustScan
Web	Burp Suite, ffuf, Gobuster
Enumeration	Netcat, WhatWeb, Nikto
Exploitation	Metasploit, Custom Scripts
Password Cracking	Hashcat, John the Ripper
Linux	LinPEAS, pspy
Windows	WinPEAS, PowerView
File Transfer	wget, curl, Python HTTP Server
Analysis	CyberChef, Python
Progress

This repository is an ongoing record of my HTB grind.

Area	Status
Starting Point	In Progress
Machines	In Progress
Challenges	In Progress
Notes & Cheatsheets	In Progress

New writeups and notes will be added as I progress.

Writeup Philosophy
Don't just collect flags.

Every HTB session should ideally answer four questions:

What did I discover?
Why did the vulnerability exist?
Why did the exploit work?
How can I recognize the same vulnerability again?

The purpose of these writeups is learning, documentation, and building a reusable security knowledge base.

Disclaimer

All testing documented in this repository is performed against Hack The Box labs and other intentionally vulnerable environments for educational purposes.

Do not use these techniques against systems without explicit authorization.

<p align="center">

Enumerate → Understand → Exploit → Learn → Repeat

<br>

The grind continues.

</p> ```
