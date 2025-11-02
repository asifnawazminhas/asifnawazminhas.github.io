---
title: Responsible Disclosure — Open Bug Bounty Outstanding Security Research Award
date: 2025-11-02 07:10:00 +0700
categories: [responsible-disclosure]
tags: [web, enumeration, information-gathering, responsible-disclosure, open-bug-bounty, appsec]
pin: false
description: "Summary of my responsible disclosure work reporting reflected XSS and open redirect issues (CVE-2021-32478) across education and learning sites worldwide, coordinated via Open Bug Bounty."
image: /assets/img/www.png
---

>  **Legal / ethical reminder:** Misusing these techniques against systems without permission is illegal. All findings described below were reported responsibly and remediated where possible.
{: .prompt-danger }

## Executive summary

Between September and November 2023 I ran a coordinated responsible-disclosure effort focused on web issues in education and learning platforms. Over the course of this work I **reported over 600 issues** 
through Open Bug Bounty and related channels; my coordinated discoveries contributed to **151+ verified fixes** acknowledged by Open Bug Bounty. 

I did not receive monetary bounties from Open Bug Bounty for most of this work, the value was in learning, coordinating fixes, and helping keep student data safer.

## Interactive map

<iframe src="/assets/maps/worldmap_interactive.html"
        width="100%" height="600" style="border:none;border-radius:8px;">
</iframe>

<p style="text-align:center;font-size:.9em;color:gray;">
Asif Minhas – Responsible Disclosure | Open Bug Bounty 2025
</p>

---

## What I found (high level)

The majority of the accepted reports I submitted to Open Bug Bounty fell into five categories:

- **Reflected Cross-Site Scripting (XSS)**
- **Open Redirect** (typically via `redirect_uri` parameters)
- **Cross-Site Request Forgery (CSRF)**
- **Improper Access Control**
- **GDPR / PII exposure**

I focused on finding:

- **Cross-Site Scripting (XSS)**
- **Open Redirect** (typically via `redirect_uri` parameters)

A particularly recurring and exploitable pattern I observed was the combination of reflected XSS + permissive `redirect_uri` handling in some LMS / LTI flows (the issue is exemplified by CVE-2021-32478).

---

## Timeline & approach

- **First accepted report on OBB:** 2023-09-07  
- **Last submission in this batch:** 2023-11-18  
- **Method:** I used a mix of automated discovery (Shodan, Censys, targeted Google dorks) and custom Python scripts to find candidate targets,
- then validated results manually to avoid false positives. Automation narrowed the search; manual validation produced clean, reproducible PoCs for that I submitted on the Open Bug Bounty platform.

---

## GitHub advisory & CVE

- **CVE:** CVE-2021-32478  
- **GitHub advisory:** <a href="https://github.com/advisories/GHSA-78fm-qhh8-8858" target="_blank" rel="noopener noreferrer">GHSA-78fm-qhh8-8858</a>

---

## Proof-of-concept 

> Note: the examples below are **sanitised** for public consumption: they use `example.com` as a placeholder. Do not publish live PoCs that target active,
> unpatched systems, always route them through the disclosure process instead.

**Reflected XSS:**

`https://example.com/mod/lti/auth.php?redirect_uri=javascript:alert(%27TEST%27)` <br>
`https://example.com/mod/lti/auth.php?redirect_uri=javascript:alert(document.domain)` <br>
`https://example.com/mod/lti/auth.php?redirect_uri=javascript:alert(document.cookie)` <br>

**Open redirect:**

`https://example.com/mod/lti/auth.php?redirect_uri=https://google.com`

---

## Impact

When combined or used in login/SSO flows, reflected XSS and open redirects can:

- Enable phishing that abuses legitimate looking login redirects
- Exfiltrate session tokens or cookies (depending on same-site policy and CSP)
- Allow attacker controlled scripts to run in the context of an educational site (risking student data or grade disclosure)

---

## Remediation checklist

For developers and IT teams maintaining learning platforms:

1. **Allowlist redirect URIs** — require exact-match redirect URIs per client, avoid accepting arbitrary external URLs.  
2. **Contextual output encoding** — never reflect untrusted input without encoding for the context (HTML/attribute/JS).  
3. **CSP & cookie policy** — implement CSP and set `SameSite`/`Secure` on session cookies.  
4. **Least privilege & access control** — verify every file endpoint and resource is properly authorised.  
5. **Automated scanning + triage** — run scheduled scans and feed results into a tracked ticketing workflow.  
6. **Disclosure contact** — publish a security contact (security@example.edu) or a disclosure page to receive reports quickly.

---

## Scope (TLDs / regions) — flags included where applicable

Below is the full list of TLDs I encountered during this research. If a TLD is country specific, I show the corresponding national flag emoji. 
If the TLD is generic / global, I mark it as **Generic**.

> **Note:** flag emoji rendering depends on the platform/browser. If the flag does not render for a reader, it will appear as two letters (regional indicator symbols).

### Country-Based TLDs with Continents

| Flag | TLD | Country / Region | Continent |
|:----:|:----|:-----------------|:-----------|
| 🇬🇧 | **.ac.uk** | United Kingdom (academic subdomain) | Europe |
| 🇦🇪 | **.ae** | United Arab Emirates | Middle East |
| 🇦🇮 | **.ai** | Anguilla (used by AI companies) | North America |
| 🇦🇷 | **.ar** | Argentina | South America |
| 🇦🇺 | **.au** | Australia | Australia |
| 🇧🇩 | **.bd** | Bangladesh | Asia |
| 🇧🇪 | **.be** | Belgium | Europe |
| 🇧🇭 | **.bh** | Bahrain | Middle East |
| 🇧🇬 | **.bg** | Bulgaria | Europe |
| 🇧🇷 | **.br** | Brazil | South America |
| 🇨🇦 | **.ca** | Canada | North America |
| 🇪🇸 | **.cat** | Catalonia (Spain region) | Europe |
| 🇨🇱 | **.cl** | Chile | South America |
| 🇨🇳 | **.cn** | China | Asia |
| 🇨🇴 | **.co** | Colombia | South America |
| 🇬🇧 | **.co.uk** | United Kingdom | Europe |
| 🇨🇾 | **.cy** | Cyprus | Europe |
| 🇩🇪 | **.de** | Germany | Europe |
| 🇪🇨 | **.ec** | Ecuador | South America |
| 🇪🇸 | **.es** | Spain | Europe |
| 🇪🇺 | **.eu** | European Union (regional) | Europe |
| 🇫🇯 | **.fj** | Fiji | Oceania |
| 🇫🇷 | **.fr** | France | Europe |
| 🇬🇷 | **.gr** | Greece | Europe |
| 🇬🇹 | **.gt** | Guatemala | North America |
| 🇭🇰 | **.hk** | Hong Kong | Asia |
| 🇭🇺 | **.hu** | Hungary | Europe |
| 🇮🇴 | **.io** | British Indian Ocean Territory (tech/startup use) | Asia |
| 🇮🇳 | **.in** | India | Asia |
| 🇮🇶 | **.iq** | Iraq | Middle East |
| 🇮🇪 | **.ie** | Ireland | Europe |
| 🇮🇹 | **.it** | Italy | Europe |
| 🇯🇴 | **.jo** | Jordan | Middle East |
| 🇯🇵 | **.jp** | Japan | Asia |
| 🇰🇿 | **.kz** | Kazakhstan | Asia |
| 🇲🇦 | **.ma** | Morocco | Africa |
| 🇲🇪 | **.me** | Montenegro | Europe |
| 🇲🇽 | **.mx** | Mexico | North America |
| 🇳🇬 | **.ng** | Nigeria | Africa |
| 🇳🇱 | **.nl** | Netherlands | Europe |
| 🇴🇲 | **.om** | Oman | Middle East |
| 🇵🇰 | **.pk** | Pakistan | Asia |
| 🇵🇸 | **.ps** | Palestine | Middle East |
| 🇵🇭 | **.ph** | Philippines | Asia |
| 🇵🇹 | **.pt** | Portugal | Europe |
| 🇷🇺 | **.ru** | Russia | Europe / Asia |
| 🇸🇦 | **.sa** | Saudi Arabia | Middle East |
| 🇹🇭 | **.th** | Thailand | Asia |
| 🇹🇷 | **.tr** | Turkey | Europe / Asia |
| 🇺🇸 | **.us** | United States | North America |
| 🇿🇦 | **.za** | South Africa | Africa |
| 🇼🇸 | **.ws** | Samoa | Oceania |

---

### Generic / Thematic TLDs

| TLD | Purpose / Theme |
|:----|:----------------|
| **.academy** | Education-themed |
| **.app** | Google-managed secure TLD |
| **.com** | Commercial / Global |
| **.dev** | Developers / Secure by default |
| **.digital** | Technology / Generic |
| **.edu** | Education (commonly US `.edu` or national equivalents) |
| **.education** | Education-themed |
| **.info** | Informational / Generic |
| **.jobs** | Employment / Career |
| **.net** | Network / Infrastructure |
| **.online** | Online presence / Global |
| **.org** | Organisation / NGO / Nonprofit |
| **.school** | Education-themed |
| **.taxi** | Services / Business |


---

## Global reach & reflection

My security research led to securing the following continents of the world:

- **Europe**  
- **North America**  
- **South America**  
- **Middle East**  
- **Africa**  
- **Asia**  
- **Australia**

I hope that I have contributed to making the online world a more secure and safe place.  
In the future, I hope to continue expanding my security research to help more organisations strengthen their defenses.

---

## Final words

I hope these coordinated disclosures helped reduce risk to students and staff.

**Acknowledgements:**  
Special thanks to **Open Bug Bounty** for their coordination and for recognising my contribution with the *Outstanding Security Research* certificate.  

## Certificate of Recognition

<img src="/assets/img/Certificate.png"
     alt="Open Bug Bounty Outstanding Security Research Certificate"
     style="display:block;margin:auto;max-width:90%;border-radius:10px;
            box-shadow:0 4px 10px rgba(0,0,0,0.15);">

<p style="text-align:center;font-size:0.9em;color:gray;">
Asif Minhas – Responsible Disclosure | Open Bug Bounty 2025  
<a href="/assets/pdf/Certificate.pdf" target="_blank" rel="noopener noreferrer">
View Full Certificate (PDF)
</a>
</p>
