---
title: "OSEP review (OffSec Experienced Penetration Tester)"
date: 2026-06-27 10:10:00 +0200
categories: [Red-teaming]
tags: [OSEP, OffSec, Red Team, Active Directory, Evasion, Windows Security]
pin: false
description: The OffSec Experienced Penetration Tester (OSEP) certification validates advanced offensive security skills in realistic enterprise environments.
image: /assets/img/OSEP-pen300.png
---

> The OffSec Experienced Penetration Tester (OSEP) certification is designed for offensive security professionals who want to move beyond traditional penetration testing. Rather than simply gaining initial access, the course emphasises operating in hardened enterprise environments, bypassing modern security controls, maintaining stealth, and achieving objectives while simulating realistic adversary behaviour.
{: .prompt-danger }

## Overview

After passing the OSCP in 2023, I wanted to continue developing my offensive security skills. Because this aligned with my day-to-day work, I decided to start the <a href="https://www.offsec.com/courses/pen-300/" target="_blank" rel="noopener noreferrer">PEN-300</a> course from <a href="https://www.offsec.com/" target="_blank" rel="noopener noreferrer">OffSec</a> in August 2025.

After several months of studying, completing the module labs, and finishing all eight challenge labs, I successfully earned the OSEP certification in June 2026.

If you've completed OSCP, PEN-300 feels like the natural next step. While PEN-200 focuses on compromising systems, PEN-300 focuses on operating inside hardened enterprise environments, bypassing common defensive controls, and performing more advanced offensive operations.

PEN-300 is a significant step up from OSCP. It introduces more advanced offensive techniques, including process injection, custom shellcode runners, application control and antivirus evasion, advanced lateral movement, and operating through command-and-control (C2) frameworks. The focus is no longer just on compromising systems, but on operating effectively within realistic enterprise environments while adapting to common defensive controls.

**I thoroughly enjoyed the course from start to finish. Every chapter introduced something new and never felt repetitive**. Almost every topic is accompanied by a dedicated lab where you can immediately apply the concepts, experiment with payloads, and become familiar with the techniques before moving on.

Although the course material clearly states that PEN-300 is an advanced penetration testing course rather than a dedicated red teaming course, I believe it provides an excellent foundation for both penetration testers and red team operators. While the course does not focus heavily on modern EDR evasion, it does teach numerous techniques for bypassing common security controls and operating effectively in hardened enterprise environments.

**In my personal opinion, OffSec offers some of the best hands-on offensive security training available today.** That doesn't take anything away from other excellent platforms such as Hack The Box, TryHackMe, PortSwigger Web Security Academy, or Zero-Point Security, all of which have their own strengths. For me, however, OffSec's emphasis on hands-on learning, realistic enterprise scenarios, and understanding the underlying techniques makes it my preferred training provider.

OffSec also positions OSEP as a certification that bridges advanced penetration testing and realistic red team operations:

<a href="https://www.offsec.com/blog/red-teaming-vs-pentesting/" target="_blank" rel="noopener noreferrer">Red Teaming vs Penetration Testing</a>

Before moving on to the syllabus, it's worth understanding where PEN-300 fits within the broader OffSec training path. Following the 200-level (foundational) courses, OffSec offers three advanced 300-level courses: <a href="https://www.offsec.com/courses/pen-300/" target="_blank" rel="noopener noreferrer">**PEN-300** (Evasion Techniques and Breaching Defenses)</a>, <a href="https://www.offsec.com/courses/web-300/" target="_blank" rel="noopener noreferrer">**WEB-300** (Advanced Web Attacks and Exploitation)</a>, and <a href="https://www.offsec.com/courses/exp-300/" target="_blank" rel="noopener noreferrer">**EXP-300** (Windows User Mode Exploit Development)</a>. These courses lead to the **OSEP**, **OSWE**, and **OSED** certifications respectively. Successfully earning all three certifications automatically awards the <a href="https://www.offsec.com/certificates/osce3/" target="_blank" rel="noopener noreferrer">OSCE³ (OffSec Certified Expert 3)</a> certification.


## Who Should Take PEN-300?

I would recommend PEN-300 to anyone who:

- Has already completed the OSCP or has equivalent hands-on penetration testing experience.
- Wants to develop advanced offensive security skills for enterprise environments.
- Is interested in operating in hardened enterprise environments.
- Wants to bridge the gap between advanced penetration testing and realistic red team operations.

If you're completely new to penetration testing, I would recommend starting with PEN-200 before tackling PEN-300.

> **Don't underestimate PEN-300.** Although it naturally builds on OSCP, the course introduces considerably more advanced concepts and expects you to understand and adapt offensive techniques rather than simply relying on publicly available tools. Make sure you're comfortable with the fundamentals covered in OSCP before starting.
{: .prompt-danger }

## Syllabus

My recommendation is to purchase the Learn One subscription if possible. The additional time allows you to work through every chapter, complete every lab, and truly understand the material instead of rushing towards the exam.

The official syllabus is available here:

<a href="https://www.offsec.com/documentation/PEN300-Syllabus.pdf" target="_blank" rel="noopener noreferrer">Official PEN-300 Syllabus</a>

Take your own notes while progressing through the course. Save useful commands, code snippets, and payloads. Once you've completed the modules, move on to the challenge labs.

One of PEN-300's biggest strengths is how it teaches you to operate in hardened enterprise environments. Rather than relying on publicly available tools, the course encourages you to understand the underlying techniques, develop your own payloads where appropriate, and adapt your approach to different defensive controls.

When I started the course, I had only a basic understanding of Active Directory and Windows payload development. By the end, I felt comfortable writing my own C# payloads, modifying Office macros, and understanding why techniques work rather than simply relying on existing tools.

I spent a considerable amount of time in Visual Studio writing payloads from scratch. That experience pushed my understanding well beyond what I had gained during OSCP and is directly applicable to real-world engagements.

## Challenge Labs

The course contains eight challenge labs with increasing difficulty. These environments closely resemble realistic enterprise networks, providing an excellent opportunity to apply the techniques learned throughout the course. Rather than attacking isolated hosts, you'll work inside interconnected Active Directory environments that encourage proper enumeration, lateral movement, and privilege escalation instead of simply exploiting individual systems.

My recommendation is to complete the challenge labs in order from one through eight before attempting the exam.

## Exam

Without giving away any spoilers, I genuinely enjoyed the exam. The objectives felt realistic and rewarded understanding rather than memorisation. There are multiple ways to obtain a passing score, allowing you to adapt your approach.

Official Exam Guide:

<a href="https://help.offsec.com/hc/en-us/articles/360050293792-OSEP-Exam-Guide" target="_blank" rel="noopener noreferrer">OSEP Exam Guide</a>

## My Preparation

My preparation was straightforward:

1. Complete every course module.
2. Finish every accompanying lab.
3. Take detailed personal notes.
4. Save useful commands, code snippets, and payloads.
5. Complete all challenge labs in order.
6. Document every attack path.

Having organised notes made revision much easier.

## Recommendation

If you already hold the OSCP or have solid hands-on penetration testing experience, I highly recommend PEN-300. It builds naturally on the knowledge gained during OSCP and takes it several steps further.

> **My recommendation:** Don't rush through the course. Complete every module, finish every lab, take detailed notes, and work through all eight challenge labs before attempting the exam. You'll get much more value from the course than if you simply study to pass the certification.
{: .prompt-tip }

### Was PEN-300 Worth It?

**Absolutely.** I enjoyed every bit of it and would happily recommend it to anyone looking to move beyond traditional penetration testing.

## Conclusion

Even after more than ten years of working as a penetration tester, I found there was still an incredible amount to learn.

PEN-300 was an excellent course. Many of the techniques covered can be applied immediately during real-world penetration tests and red team engagements.

Perhaps the biggest strength of the course is that it teaches the thought process behind offensive operations instead of simply providing tools. Rather than relying solely on existing tooling, you learn why techniques work and how to adapt your approach to different environments.

Most importantly, much of the content transfers directly to real-world penetration tests and realistic enterprise environments, which is exactly what I was looking for.

Whether your goal is to become a stronger penetration tester or prepare for realistic enterprise red team engagements, PEN-300 is a course that I can wholeheartedly recommend.

> **Tip:** If your budget allows it, choose the Learn One subscription. Having a full year gives you enough time to complete the modules, challenge labs, and revisit difficult topics without unnecessary time pressure.
{: .prompt-tip }

## Final Thoughts

Compared to the OSCP, PEN-300 goes significantly deeper into advanced offensive techniques, payload development, Active Directory, bypassing common security controls, and operating within hardened enterprise environments. **While OSCP teaches you how to compromise systems, PEN-300 teaches you how to operate effectively within enterprise environments once you've gained access**.

**For me, this was one of the most enjoyable and practical offensive security courses I've completed**. Much of what I learned can be applied directly during real-world penetration tests, making it one of the most valuable certifications I've earned so far.

## Additional Resources 

During my preparation, I came across many excellent blogs, cheat sheets, and reference websites. Below are some of the resources that I personally found the most helpful throughout my PEN-300 journey.

- <a href="https://lolad-project.github.io/" target="_blank" rel="noopener noreferrer">LOLAD Project</a>
- <a href="https://www.emmanuelsolis.com/osep.html" target="_blank" rel="noopener noreferrer">OSEP Notes - Emmanuel Solis</a>
- <a href="https://steffinstanly.gitbook.io/osep-notes/osep/privilege-escalation" target="_blank" rel="noopener noreferrer">Steffin Stanly OSEP Notes</a>
- <a href="https://commandmgr.com/" target="_blank" rel="noopener noreferrer">Command Manager</a>
- <a href="https://github.com/Extravenger/OSEPlayground" target="_blank" rel="noopener noreferrer">OSEP Playground</a>
- <a href="https://ippsec.rocks/" target="_blank" rel="noopener noreferrer">IppSec</a>
- <a href="https://0xdf.gitlab.io/" target="_blank" rel="noopener noreferrer">0xdf</a>
- <a href="https://www.revshells.com/" target="_blank" rel="noopener noreferrer">Reverse Shells</a>
- <a href="https://hacktricks.wiki/en/windows-hardening/active-directory-methodology/index.html" target="_blank" rel="noopener noreferrer">HackTricks Active Directory</a>
- <a href="https://anshu19981.github.io/Pentestcheatsheet/" target="_blank" rel="noopener noreferrer">Pentest Cheat Sheet</a>
- <a href="https://www.netexec.wiki/" target="_blank" rel="noopener noreferrer">NetExec Wiki</a>


## Certificate

Thanks for taking the time to read my review. I hope it helps you decide whether PEN-300 is the right next step in your offensive security journey.

You can verify my OSEP certification by clicking on the certificate below.

<p align="center">
  <a href="https://credentials.offsec.com/0efe3aa1-6299-4800-a706-6c7ef79c5f6c#acc.JH1g5xBR"
     target="_blank"
     rel="noopener noreferrer">
    <img src="https://api.accredible.com/v1/frontend/credential_website_embed_image/certificate/186059714"
         alt="OSEP Certificate"
         style="max-width:100%; height:auto;">
  </a>
</p>

