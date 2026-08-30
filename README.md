Hack The Box — Grind & Writeups

<p align="center">
  <img src="https://img.shields.io/badge/Platform-Hack%20The%20Box-9FEF00?style=for-the-badge&logo=hackthebox&logoColor=white" alt="Hack The Box">
  <img src="https://img.shields.io/badge/Focus-Cybersecurity-111111?style=for-the-badge&logo=hackthebox&logoColor=9FEF00" alt="Cybersecurity">
  <img src="https://img.shields.io/badge/Status-In%20Progress-orange?style=for-the-badge" alt="Status">
</p><p align="center">
  <b>Learning offensive security through hands-on labs, one machine at a time.</b>
</p>---

About This Repository

This repository contains my Hack The Box writeups, notes, commands, methodologies, and lessons learned while working through HTB machines and challenges.

The goal isn't simply to collect flags.

«The goal is to understand why the attack worked.»

Each writeup documents the process from initial reconnaissance to enumeration, exploitation, privilege escalation, and flag retrieval.

---

What I'm Learning

This grind is helping me build practical skills in:

- Reconnaissance & Enumeration
- Web Application Security
- Linux Privilege Escalation
- Windows Privilege Escalation
- Authentication & Access Control
- Command Injection
- Server-Side Vulnerabilities
- Database Enumeration
- Network Enumeration
- Exploit Analysis
- Post-Exploitation
- Vulnerability Research
- Offensive Security Methodology

---

Repository Structure

HTB-Writeups/
│
├── README.md
│
├── Starting Point/
│   │
│   ├── Command/
│   │   ├── README.md
│   │   └── screenshots/
│   │
│   └── ...
│
├── Machines/
│   │
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

---

Methodology

Rather than immediately searching for an exploit, I try to follow a repeatable methodology.

                         TARGET
                           │
                           ▼
                  RECONNAISSANCE
                           │
                           ▼
                     ENUMERATION
                           │
                           ▼
                  VULNERABILITY
                    DISCOVERY
                           │
                           ▼
                    EXPLOITATION
                           │
                           ▼
                    INITIAL ACCESS
                           │
                           ▼
                  PRIVILEGE
                  ESCALATION
                           │
                           ▼
                     FLAGS / PROOF

1. Reconnaissance

First, understand the target.

Typical checks:

nmap
rustscan
whatweb
nikto

Questions to answer:

- What ports are open?
- What services are running?
- What technologies are being used?
- Is there a web application?
- Are there interesting hostnames or domains?

---

2. Enumeration

Once services are identified, enumerate them deeply.

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

---

3. Vulnerability Discovery

The goal is to connect enumeration results to a possible attack path.

Examples:

Exposed Credentials
Misconfigured Services
Weak Authentication
Command Injection
SQL Injection
File Inclusion
File Upload Vulnerabilities
Information Disclosure
Privilege Escalation Vectors

---

4. Exploitation

Validate the vulnerability and obtain the required access.

I try to understand:

«Why does this payload work?»

rather than simply copying an exploit.

---

5. Initial Access

After gaining access:

Identify Current User
        │
        ▼
Enumerate Environment
        │
        ▼
Find Credentials / Secrets
        │
        ▼
Enumerate Local Services
        │
        ▼
Search for Privilege Escalation

---

6. Privilege Escalation

Depending on the operating system.

Linux:

sudo -l
find / -perm -4000 2>/dev/null
getcap -r / 2>/dev/null

Windows:

whoami /all
systeminfo
net user
net localgroup administrators

The exact commands used in each writeup will depend on the target.

---

Common Tools

Area| Tools
Recon| Nmap, RustScan
Web| Burp Suite, ffuf, Gobuster
Enumeration| Netcat, WhatWeb, Nikto
Exploitation| Metasploit, Custom Scripts
Passwords| Hashcat, John the Ripper
Linux| LinPEAS, pspy
Windows| WinPEAS, PowerView
File Transfer| wget, curl, Python HTTP Server
Analysis| CyberChef, Python

---

Progress

HTB GRIND
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Starting Point    ███░░░░░░░░░░░░░░░░░  In Progress
Machines          ░░░░░░░░░░░░░░░░░░░░░  Not Started
Challenges        ░░░░░░░░░░░░░░░░░░░░░  Not Started

Goal: Build practical offensive-security skills.

Progress will be updated as new machines and challenges are completed.

---

Writeup Philosophy

Don't just collect flags.

A successful HTB session should leave me with at least one new concept.

What did I learn?
       │
       ▼
Why did it work?
       │
       ▼
How would I recognize it again?
       │
       ▼
How could I exploit it on another target?

The purpose of these writeups is learning and documentation, not simply reproducing solutions.

---

Disclaimer

All testing documented in this repository is performed against Hack The Box labs and other intentionally vulnerable environments for educational purposes.

Do not use these techniques against systems without explicit authorization.

---

<p align="center">Enumerate → Understand → Exploit → Learn → Repeat

The grind continues.

</p>
