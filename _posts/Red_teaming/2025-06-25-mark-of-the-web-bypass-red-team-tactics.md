---
title: "Mark of the Web Bypass - Red Team Tactics"
date: 2025-06-25 18:30:00 +0200
categories: [Payload-Delivery]
tags: [Red Teaming, MOTW, Phishing, ISO, SmartScreen, Detection Evasion, T1553.005]
toc: true
pin: false
---

# Mark of the Web Bypass – Red Team Tactics

This post demonstrates how red team operators can bypass **Mark of the Web (MotW)** protections to achieve stealthy execution of payloads on Windows systems. It aligns with [MITRE ATT&CK T1553.005 – Mark-of-the-Web Bypass](https://attack.mitre.org/techniques/T1553/005/){:target="_blank"} and includes practical steps, payload techniques, and detection insights.

---

## Overview

When a file is downloaded from the Internet or received via email, Windows adds a hidden **Zone.Identifier** Alternate Data Stream (ADS) that tags it with the **"Internet Zone"**.

This metadata is used by:
- Microsoft Office (Protected View)
- Windows SmartScreen
- Windows Defender
- Script interpreters (PowerShell, WSH, etc.)

Execution is blocked, restricted, or prompts the user.

---

## 📁 MotW Bypass Strategy (Step-by-Step)

### ✅ Step 1: Create Your Payload

Prepare any executable or script you want to deliver, commonly `.ps1`, `.vbs`, `.js`, `.bat`, or `.exe`.

```powershell
echo 'Write-Output "Bypassed MOTW"' > payload.ps1
```

---

### ✅ Step 2: Archive the Payload (ZIP)

Compress the payload into a `.zip` or `.7z` archive. This is your delivery container.

```bash
7z a payload.zip payload.ps1
```

MotW is applied to `payload.zip` if downloaded, but **not automatically to extracted files**.

---

### ✅ Step 3: Deliver the Archive

Send the archive via:
- Phishing email attachment
- Malicious web server
- USB drop
- Social engineering download (e.g., fake CV)

Once received, MotW will be on the **container**, not the **contents** (depending on how it’s extracted).

---

### ✅ Step 4: Ensure Extraction Bypasses MotW

Have the user extract the archive with a third-party tool like **7-Zip**.

```bash
7z x payload.zip
```

> ✅ Files extracted with 7-Zip often **don’t inherit MotW**  
> ❌ Files extracted with Windows Explorer **do inherit MotW**

---

### ✅ Step 5: Execute Payload Without Prompt

Once extracted without MotW, the payload (e.g., `payload.ps1`) will execute **without Protected View**, **no SmartScreen warning**, and **no execution block**.

```powershell
.\payload.ps1
```

---

## 🧪 Full Execution Flow

| Phase          | Action                         | Tool/Technique                   |
|----------------|--------------------------------|----------------------------------|
| Preparation    | Generate `payload.ps1`         | PowerShell                       |
| Containerize   | `payload.ps1` → `payload.zip`  | 7-Zip                            |
| Delivery       | Email or link download         | Phishing, USB, Web               |
| Extraction     | 7-Zip extracts without MotW    | Archive utility                  |
| Execution      | Run `payload.ps1`              | Stealthy, no prompts             |

---

## 🔐 MITRE ATT&CK Mapping

- **Technique**: [T1553.005 – Mark-of-the-Web Bypass](https://attack.mitre.org/techniques/T1553/005/){:target="_blank"}
- **Tactic**: Defense Evasion
- **Description**: Adversaries may remove or bypass MotW to prevent execution restrictions on downloaded files.

---

## 📦 Filetypes Targeted by MotW

These file types are either blocked or restricted when tagged with MotW:

```
.ade, .adp, .app, .asp, .bas, .bat, .cer, .chm, .cmd, .com, .cpl, .crt, .csh, .der, .dll,
.exe, .fxp, .gadget, .hlp, .hta, .htm, .html, .img, .inf, .ins, .iso, .isp, .its,
.js, .jse, .ksh, .lnk, .msc, .mad, .maf, .mag, .mam, .maq, .mar, .mas, .mat, .mau,
.mav, .maw, .mcf, .mda, .mdb, .mde, .mdt, .mdw, .mdz, .msh, .msh1, .msh2, .msp,
.mst, .msu, .ops, .pcd, .pl, .plg, .prg, .ps1, .ps1xml, .ps2, .psc1, .psc2, .psd1,
.psm1, .pst, .scf, .sct, .shb, .shs, .tmp, .url, .vbe, .vbs, .vbp, .vhd, .vhdx,
.vsmacros, .vsw, .ws, .wsc, .wsf, .wsh, .xnk
```

Many of these are exploited by adversaries for phishing, scripting, and lateral movement payloads.

Reference: [Red Canary MOTW Report](https://redcanary.com/threat-detection-report/techniques/mark-of-the-web-bypass/){:target="_blank"}

---

## 🕵️‍♀️ Blue Team Detection Tips

| Technique         | Detection Source                   |
|------------------|------------------------------------|
| ADS creation      | Sysmon Event ID 15                 |
| ISO mount events  | Microsoft-Windows-VHDMP logs       |
| Zone reads        | Windows Defender/SmartScreen logs  |
| File execution    | AMSI, Script Block Logging         |

🧩 Additional monitoring:
- Uncommon mounting of `.iso` or `.vhd` from downloads
- Execution of high-risk extensions with no MotW present

---

## 🧠 Notes and Limitations

- **Not all extraction tools behave the same.** Always test `.zip` and `.iso` behavior in your target's environment.
- **Office macros** may still be blocked unless inside a trusted location.
- **Smart AV solutions** may analyze content even without MotW.

---

## ✅ Summary

| Step     | Technique                     |
|----------|-------------------------------|
| 1        | Create payload script         |
| 2        | Archive in `.zip` or `.iso`   |
| 3        | Deliver via phishing/web      |
| 4        | Extract using 7-Zip           |
| 5        | Execute payload silently      |

---

Stay sharp. Simulate real adversary behavior. And always disclose responsibly.
