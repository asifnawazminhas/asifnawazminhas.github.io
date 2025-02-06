---
title: "CRTO review (Certified Red Team Operator)"
date: 2025-02-05 10:10:00 +0700
categories: [Red-teaming]
tags: [TTP, adversary simulation, red-team, Cobalt Strike]
pin: false
---

![Red Team Operations](assets/img/red_team_ops.png)

![CRTO1](assets/img/crto1.png)

> Red Teaming is a cybersecurity assessment that involves adversary simulation, using real-world TTPs (Tactics, Techniques, and Procedures) to mimic advanced threats, test defenses, and improve an organisation's security posture.
{: .prompt-danger }

## Overview
<span style="color:#00FF00;">➡️</span>
I've earned the <a href="https://eu.badgr.com/public/assertions/p2xxJx0DQU2ffXblBNslLg" target="_blank">CRTO badge</a> from <a href="https://training.zeropointsecurity.co.uk/" target="_blank">Zero-Point Security</a>.
What I really liked about this course is that it has a good mix of explainations and doing hands on exercises in the snaplabs environment.

The **Certified Red Team Operator** certification is an **advanced course in offensive security**, focusing primarily on the use of **Cobalt Strike**, one of the main trade tools. The **course, exam, and all aspects of Zero Point Security are overseen by RastaMouse**.

## Key takeawasy from the course
✅ **Highly Recommended** – Covers essential Red Team concepts.  
✅ **Hands-on Experience** – Work with **Cobalt Strike C2** in a fully functional lab.  
✅ **Lifetime Access** – Get free course updates and revisit materials anytime.  

## Simulation real world attacks

> The course teaches <a href="https://attack.mitre.org/tactics/enterprise/" target="_blank">tactics</a>, 
<a href="https://attack.mitre.org/techniques/enterprise/" target="_blank">techniques</a>, and 
<a href="https://attack.mitre.org/groups/" target="_blank">procedures (TTPs)</a> to **simulate real-world threats**, 
allowing security teams to test and enhance their detection and response capabilities while strengthening an organisation's overall security posture.

## Understanding TTPs (Tactics, Techniques, and Procedures)

TTPs describe how attackers behave when compromising a system or network. Understanding them is **crucial for effective defense**. They are broken down into three categories:

### **1️⃣ Tactics**
These are the **high-level strategic goals** of the attacker. They represent **what** the attacker is trying to achieve. Examples include:
- **Privilege Escalation**
- **Lateral Movement**
- **Persistence**
- **Data Exfiltration**
- **Initial Access**

### **2️⃣ Techniques**
These are the **specific methods or tools** used to accomplish a tactic. They describe **how** the attacker achieves their goal. Examples include:

- **Privilege Escalation:** Kerberoasting, Pass-the-Hash, Exploiting known vulnerabilities.
- **Lateral Movement:** Pass-the-Hash, Remote Services, Valid Accounts.
- **Persistence:** Scheduled Tasks, Registry Keys, Backdoors.
- **Data Exfiltration:** FTP, HTTPS, DNS Tunneling.
- **Initial Access:** Phishing, Exploit public facing application.

### **3️⃣ Procedures**
These are the **step-by-step implementations** of specific techniques within a particular attack. They describe the **specific way** an attacker uses a technique in a given scenario. 

Procedures are often **unique to an attacker or campaign** and provide the most granular level of detail. For example, a procedure might describe the **exact sequence of commands** used in a **Pass-the-Hash** attack, including the specific tools and parameters executed.

---

### **Summary:**
- **Tactics:** The attacker's **goal**.
- **Techniques:** The **methods** used to achieve the goal.
- **Procedures:** The **specific implementation** of a technique.

By understanding **TTPs**, defenders can **better anticipate, detect, and respond** to attacks, strengthening overall security posture.

## Lab Environment

You have **lifetime access** to the lab environment, hosted on the <a href="https://www.thinkific.com/" target="_blank">Thinkific</a> platform.
The interface is well-structured, making it **smooth and intuitive** to navigate between modules.
The labs are hosted on **SnapLabs** and accessed via **Guacamole** in your web browser. I would recommended to use the **Google Chrome** browser, as it is likely to provide a **smoother experience** than other browsers.

## Exam Format: CTF-Style Challenge

One aspect of the **CRTO exam** that I really liked is its **CTF-style format**. The exam consists of **8 flags**, and you need to capture **at least 6** to pass. Each flag represents a critical step in the attack chain, such as **initial access, lateral movement, privilege escalation, or data exfiltration**.

Once you obtain a flag, you submit it in the **exam portal**, and your progress is instantly updated, this looks great! **No report writing, no documentation, needed to do, just pure technical execution.** If you collect **6 or more flags by the end of the exam**, you will pass. It's a **straightforward, hands-on, and practical approach** that focuses purely on **offensive security skills**.

The **course material starts off strong**, simulating a **full Red Team engagement** with **Defender both enabled and disabled**. **RastaMouse** has written the material **very concisely and relevantly**, ensuring it remains **practical and effective** for learners.

## Key Topics

### 1️⃣ Command & Control (C2)
- Setting up and managing **Cobalt Strike**.
- Generating **payloads** and establishing **beacon communication**.
- Understanding **OPSEC considerations** to maintain stealth.

### 2️⃣ Privilege Escalation & Credential Theft
- Exploiting **Windows services** for **privilege escalation**.
- **Bypassing UAC** to gain higher privileges.
- Extracting credentials using **Mimikatz**, NTLM hashes, and Kerberos tickets.

### 3️⃣ Bypassing Defenses & OPSEC
- **Evading Windows Defender, AppLocker, and AMSI**.
- Understanding **common detection techniques** and how to **stay under the radar**.

### 4️⃣ Lateral Movement & User Impersonation
- Moving laterally within a network using **PsExec, WMI, and WinRM**.
- Using **Pass-the-Hash (PtH), Pass-the-Ticket (PtT)**, and **Kerberos ticket impersonation**.

### 5️⃣ Post-Exploitation & Data Exfiltration
- **Gathering sensitive data** while maintaining stealth.
- Techniques for **exfiltrating data** without triggering alarms.

## Strategy for Success

A **strong approach** to the course is to **completely read through each section** and **create notes** as needed. The content is structured **logically** and designed to be **absorbed progressively**. This is also what I've done and would recommend to do.

## Additional Topics
Other important topics such as **host persistence**, **domain reconnaissance**, and **Active Directory enumeration** are covered in detail in the **full curriculum**. For a complete breakdown of the course structure, visit: <a href="https://training.zeropointsecurity.co.uk/courses/red-team-ops" target="_blank">CRTO Course Page</a>

## Final Thoughts

The **CRTO course** is one of the best ways to gain **practical experience** in **Active Directory exploitation and adversary simulation**. It provides a **structured approach** to **Red Team operations** and helps refine **stealth tactics** used in real-world engagements.

🚀 **Highly recommended for anyone looking to advance their Red Teaming skills!**

<div class="badgr-badge" style="font-family: Helvetica, Roboto, 'Segoe UI', Calibri, sans-serif; background-color: #f0f0f0; padding: 10px; border-radius: 8px;">
    <a href="https://api.eu.badgr.io/public/assertions/p2xxJx0DQU2ffXblBNslLg" target="_blank">
        <img width="120px" height="120px" src="https://api.eu.badgr.io/public/assertions/p2xxJx0DQU2ffXblBNslLg/image" 
             alt="CRTO Badge - Red Team Operator" style="display: block; margin: 0 auto;">
    </a>
    <p class="badgr-badge-name" style="font-size: 16px; font-weight: 600; text-align: center; color: #333;">
        Red Team Operator
    </p>
    <p class="badgr-badge-date" style="font-size: 12px; text-align: center; color: #444;">
        <strong style="color: #222;">Awarded:</strong> 5 Feb 2025
    </p>
    <p class="badgr-badge-recipient" style="font-size: 12px; text-align: center; color: #444;">
        <strong style="color: #222;">Awarded To:</strong> <span>Asif Nawaz Minhas</span>
    </p>
    <p style="margin: 16px 0; padding: 0; text-align: center;">
        <a class="badgr-badge-verify" target="_blank" 
           href="https://badgecheck.io?url=https%3A%2F%2Fapi.eu.badgr.io%2Fpublic%2Fassertions%2Fp2xxJx0DQU2ffXblBNslLg" 
           style="font-size:14px; font-weight: bold; border: solid 1px #333; text-decoration: none; 
                  padding: 6px 16px; color: #222; background-color: #ddd; border-radius: 4px;">
            VERIFY
        </a>
    </p>
</div>
<script async="async" src="https://eu.badgr.com/assets/widgets.bundle.js"></script>

## Useful resources

- [Community Kit](https://cobalt-strike.github.io/community_kit/){:target="_blank"}
- [Red Team Notes 2.0](https://dmcxblue.gitbook.io/red-team-notes-2-0){:target="_blank"}
- [A Simple Guide to Successful Red Teaming](https://www.cobaltstrike.com/resources/guides/a-simple-guide-to-successful-red-teaming){:target="_blank"}
- [Offensive Security Cheat Sheets](https://www.ired.team/offensive-security-experiments/offensive-security-cheetsheets){:target="_blank"}
- [Cobalt Strike Kits References](https://swisskyrepo.github.io/InternalAllTheThings/command-control/cobalt-strike-kits/#references){:target="_blank"}
- [The C2 Matrix](https://ask.thec2matrix.com/){:target="_blank"}
- [Red Team Guide](https://redteam.guide/docs/definitions/){:target="_blank"}
- [Red Team Wiki - C2](https://redteam.wiki/en/misc/c2){:target="_blank"}
- [ART Toolkit](https://arttoolkit.github.io/){:target="_blank"}
- [Atomic Red Team](https://www.atomicredteam.io/atomic-red-team){:target="_blank"}
- [Red Team Certifications - Notes & Cheat Sheets](https://github.com/0xn1k5/Red-Teaming/blob/main/Red%20Team%20Certifications%20-%20Notes%20%26%20Cheat%20Sheets/CRTO%20-%20Notes%20%26%20Cheat%20Sheet.md){:target="_blank"}
