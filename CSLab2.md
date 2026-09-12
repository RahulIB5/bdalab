# PGM 3 — SQL Injection Using DVWA

## Aim
To demonstrate SQL Injection vulnerabilities using DVWA and understand how improper input validation can expose sensitive database information.

---

## Software Required

- Oracle VirtualBox
- Kali Linux
- Apache2
- MariaDB/MySQL
- PHP
- DVWA (Damn Vulnerable Web Application)
- Firefox Browser

---

# Procedure

## 1. Start Kali Linux

1. Open **Oracle VirtualBox**.
2. Select **Kali Linux**.
3. Click **Start**.
4. Login to Kali Linux.

---

## 2. Open Terminal

Open the **Terminal** in Kali Linux.

---

## 3. Update System

```bash
sudo apt update
````

---

## 4. Install and Start Apache

Check Apache:

```bash
apache2 -v
```

If not installed:

```bash
sudo apt install apache2 -y
```

Start Apache:

```bash
sudo systemctl start apache2
```

Enable Apache:

```bash
sudo systemctl enable apache2
```

Check status:

```bash
sudo systemctl status apache2
```

Expected:

```text
Active: active (running)
```

---

## 5. Install and Start MariaDB

Install:

```bash
sudo apt install mariadb-server -y
```

Start:

```bash
sudo systemctl start mariadb
```

Enable:

```bash
sudo systemctl enable mariadb
```

Check:

```bash
sudo systemctl status mariadb
```

---

## 6. Install PHP

```bash
sudo apt install php php-mysql libapache2-mod-php -y
```

Check PHP:

```bash
php -v
```

---

## 7. Install Git

```bash
sudo apt install git -y
```

---

## 8. Download DVWA

Go to Apache's web directory:

```bash
cd /var/www/html
```

Remove the default page:

```bash
sudo rm index.html
```

Clone DVWA:

```bash
sudo git clone https://github.com/digininja/DVWA.git
```

---

## 9. Configure DVWA

Go to the configuration directory:

```bash
cd /var/www/html/DVWA/config
```

Copy the sample configuration:

```bash
sudo cp config.inc.php.dist config.inc.php
```

---

## 10. Create DVWA Database

Open MariaDB:

```bash
sudo mysql
```

Create database:

```sql
CREATE DATABASE dvwa;
```

Create user:

```sql
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'p@ssw0rd';
```

Grant permissions:

```sql
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
```

Reload privileges:

```sql
FLUSH PRIVILEGES;
```

Exit:

```sql
EXIT;
```

---

## 11. Edit DVWA Configuration

Open:

```bash
sudo nano /var/www/html/DVWA/config/config.inc.php
```

Set the database values to:

```php
$_DVWA['db_server'] = '127.0.0.1';
$_DVWA['db_database'] = 'dvwa';
$_DVWA['db_user'] = 'dvwa';
$_DVWA['db_password'] = 'p@ssw0rd';
```

Save:

```text
Ctrl + O
Enter
```

Exit:

```text
Ctrl + X
```

---

## 12. Give Permissions

Run:

```bash
sudo chmod -R 777 /var/www/html/DVWA/hackable/uploads
```

Then:

```bash
sudo chmod -R 777 /var/www/html/DVWA/config
```

---

## 13. Restart Apache

```bash
sudo systemctl restart apache2
```

---

# DVWA Setup

## 14. Open DVWA

Open Firefox and go to:

```text
http://localhost/DVWA
```

If the **Database Setup** page appears:

1. Scroll down.
2. Click **Create / Reset Database**.
3. Wait for the database setup to complete.
4. Proceed to the login page.

---

## 15. Login

Use:

```text
Username: admin
Password: password
```

Click **Login**.

---

## 16. Set Security Level

From the left menu:

```text
DVWA Security
```

Set:

```text
Security Level: Low
```

Click:

```text
Submit
```

---

# SQL Injection Demonstration

## 17. Open SQL Injection

From the left menu, click:

```text
SQL Injection
```

You should see:

```text
User ID: [          ] [Submit]
```

---

## 18. Test Normal Input

Enter:

```text
1
```

Click **Submit**.

Expected result:

```text
User ID 1 information
```

In our test, the result displayed:

```text
Username: admin
First name: admin
```

This demonstrates normal application behavior for a valid input.

---

## 19. Test SQL Injection

In the same **User ID** field, enter:

```text
'
```

Click **Submit**.

Expected behavior may be an SQL error or other unexpected behavior.

In our test, the browser displayed:

```text
500 Internal Server Error
```

This demonstrates improper handling of the input.

---

## 20. Observe and Record Results

Record:

* Returned user information for valid input.
* Error messages for invalid/malicious input.
* Difference between valid and invalid input.

### Observation

| Input | Result                     |
| ----- | -------------------------- |
| `1`   | User information displayed |
| `'`   | 500 Internal Server Error  |

### Inference

The application behaves unexpectedly when a special SQL character is supplied, indicating inadequate input handling and demonstrating the SQL Injection vulnerability.

---

# Prevention / Secure Coding Practices

SQL Injection can be prevented using:

1. **Prepared / parameterized statements**
2. **Input validation**
3. **Least-privilege database accounts**
4. **Hiding detailed database error messages**
5. **Regular security testing**

---

# Shutdown

After completing the experiment, stop Apache:

```bash
sudo systemctl stop apache2
```

Stop MariaDB:

```bash
sudo systemctl stop mariadb
```

Verify Apache:

```bash
sudo systemctl status apache2
```

Verify MariaDB:

```bash
sudo systemctl status mariadb
```

---

# Result

The SQL Injection vulnerability was successfully demonstrated using DVWA. A normal input (`1`) returned user information, while a special SQL character (`'`) caused unexpected server behavior/HTTP 500 error. This demonstrates how improper input validation can expose an application to SQL Injection vulnerabilities.

---

# Quick Revision — Commands Only

```bash
# Update
sudo apt update

# Apache
apache2 -v
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
sudo systemctl status apache2

# MariaDB
sudo apt install mariadb-server -y
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo systemctl status mariadb

# PHP
sudo apt install php php-mysql libapache2-mod-php -y
php -v

# Git
sudo apt install git -y

# DVWA
cd /var/www/html
sudo rm index.html
sudo git clone https://github.com/digininja/DVWA.git

# Configuration
cd /var/www/html/DVWA/config
sudo cp config.inc.php.dist config.inc.php

# Database
sudo mysql
```

```sql
CREATE DATABASE dvwa;
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'p@ssw0rd';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

```bash
# Configure
sudo nano /var/www/html/DVWA/config/config.inc.php

# Permissions
sudo chmod -R 777 /var/www/html/DVWA/hackable/uploads
sudo chmod -R 777 /var/www/html/DVWA/config

# Restart
sudo systemctl restart apache2
```

```text
Firefox
→ http://localhost/DVWA
→ Create / Reset Database
→ Login: admin / password
→ DVWA Security → Low → Submit
→ SQL Injection
→ Input: 1 → Submit
→ Input: ' → Submit
→ Record results
```

```bash
# Shutdown
sudo systemctl stop apache2
sudo systemctl stop mariadb
sudo systemctl status apache2
sudo systemctl status mariadb
```

**This follows the complete sequence in your uploaded PGM 3 document, including the setup, DVWA configuration, SQL Injection test, observations, prevention points, and shutdown.**      

```
```
