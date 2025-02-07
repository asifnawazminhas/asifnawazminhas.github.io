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
What I really liked about this course is that it has a good mix of explanations and hands-on exercises in the SnapLabs environment.

The **Certified Red Team Operator** certification is an **advanced course in offensive security**, focusing primarily on the use of **Cobalt Strike**, one of the main trade tools. The **course, exam, and all aspects of Zero Point Security are overseen by RastaMouse**.

## Key Takeaways from the Course
✅ **Highly Recommended** – Covers essential Red Team concepts.  
✅ **Hands-on Experience** – Work with **Cobalt Strike C2** in a fully functional lab.  
✅ **Lifetime Access** – Get free course updates and revisit materials anytime.  

## What I Really Liked About CRTO  

One of the best parts of this course was the deep hands-on experience with **Cobalt Strike and Red Teaming methodologies**. Here’s what stood out:  

- **Hands-on Experience with Cobalt Strike C2** – Practical exercises covering **beacon management, payload staging, and real-world attack simulation**.  
- **Working with Artifacts in Cobalt Strike** – Customizing **artifact kits** to improve **payload OPSEC and evasion**.  
- **Building a Custom C2 Profile** – Crafting a **Malleable C2 profile** and learning how adversaries **mimic legitimate traffic**.  
- **Bypassing Windows Defender** – Learning the **methodology** of systematically bypassing **Windows Defender.**.  
- **Understanding Red Teaming Configurations** – Gaining insight into **Cobalt Strike infrastructure, OPSEC configurations, and stealth techniques**.  
- **No Flag Submission Needed** – The exam is **purely technical**, and if you capture **at least 6 flags**, you **pass automatically**—**no need to submit a report!**  
- **Get Your Digital Certificate** – Once you pass, **create an account on [Badgr](https://eu.badgr.com/){:target="_blank"} with the same email address you registered at [training.zeropointsecurity.co.uk](https://training.zeropointsecurity.co.uk/){:target="_blank"} to claim your digital certificate**.  


---

## Simulation of Real-World Attacks

> The course teaches <a href="https://attack.mitre.org/tactics/enterprise/" target="_blank">tactics</a>, 
<a href="https://attack.mitre.org/techniques/enterprise/" target="_blank">techniques</a>, and 
<a href="https://attack.mitre.org/groups/" target="_blank">procedures (TTPs)</a> to **simulate real-world threats**, 
allowing security teams to test and enhance their detection and response capabilities while strengthening an organisation's overall security posture.

## Who Should Take CRTO?  

This course is **ideal for professionals looking to gain practical Red Teaming experience**, especially with **Cobalt Strike**.

✅ **Penetration Testers** who want to transition into **Red Teaming**.  
✅ **Security Analysts & SOC Teams** who want to understand **real-world adversary techniques**.  
✅ **Aspiring Red Teamers** who want to master **Cobalt Strike, OPSEC, and evasion**.  
✅ **Defensive Security Professionals** who want insight into **how attackers bypass security controls**.  

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

🔹 **For those who want to dive deeper into Red Teaming, here are some additional resources:**  

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

---
🚀 **CRTO is one of the best practical Red Teaming certifications out there—highly recommended!**  

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
 
