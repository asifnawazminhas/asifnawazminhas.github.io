---
title: "Subdomain takeover: A deep dive into a common but overlooked cloud vulnerability"
date: 2025-07-01
author: "Asif Nawaz Minhas"
tags: [subdomain takeover, red teaming, DNS, CNAME, cloud security, offensive security]
---

> ⚠️ **Disclaimer**  
> This blog post is for **educational and ethical hacking purposes only**. The author is **not responsible** for any misuse of the techniques described. 
Always seek **explicit permission** before testing domains or infrastructure that you do not own or operate. Any techniques discussed are intended 
solely for **authorized red teaming, penetration testing**, and **responsible disclosure** efforts.

---

## Why subdomain takeover matters in red teaming and beyond

In red teaming or offensive security simulations, the goal is to think like an attacker - and attackers love low hanging fruit(s). 
One such vector is the **subdomain takeover**: it’s **stealthy, high-impact, and surprisingly common**, especially in cloud environments.

Subdomain takeovers often don’t require advanced exploit chains. They prey on **poor hygiene**, leftover DNS records, abandoned cloud services 
and lack of visibility. That’s why red teamers, bug bounty hunters, and cloud security engineers alike must understand and monitor for them.

---

## What is a subdomain takeover?

A **subdomain takeover** occurs when a subdomain (like `staging.example.com`) points via DNS to a service 
(like an Azure App, GitHub Pages site, Heroku app, S3 bucket, etc.) that has **not been claimed** or was **deleted but not cleaned up** in DNS.

An attacker who identifies this dangling subdomain can **register the same resource** on the third party platform and take control of that subdomain
and essentially hijacking the namespace under a trusted parent domain.

### How subdomain takeovers work

- A subdomain has a `CNAME` record pointing to `orphaned-service.cloudprovider.com`
- The target (`orphaned-service`) is unclaimed or deleted
- The `CNAME` still exists and resolves
- Attacker claims the orphaned service and serves content from the subdomain

---

## The DNS + CNAME relationship in This Attack

At the core of this vulnerability is how DNS resolution works — particularly with **CNAME records**.

- A **CNAME (Canonical Name)** record is an alias that maps one domain to another.
- Example:
  
  ```
  staging.example.com. 300 IN CNAME staging-app.azurewebsites.net.
  ```

If `staging-app.azurewebsites.net` is deleted but the above DNS record remains, `staging.example.com` becomes a dangling pointer, thus waiting to be claimed.

This is what we mean when we say "a dangling CNAME."

---

## How to detect a vulnerable subdomain

**What is NXDOMAIN?**  
> It means "Non-Existent Domain", DNS attempted to resolve a domain name but found no valid target. If your subdomain’s CNAME points to a service that returns NXDOMAIN, it’s likely vulnerable to takeover.

Here’s how you can **manually check** whether a subdomain is vulnerable:

### Step 1: Check the CNAME record

```bash
dig subdomain.example.com
```

Look for a response like:

```
;; ANSWER SECTION:
subdomain.example.com.  300 IN CNAME poc.azurewebsites.net.
```

### Step 2: Check if the CNAME target is available or dead

```bash
dig poc.azurewebsites.net
```

If you see:

```
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 12345
```

That means the CNAME target **does not exist**, a key indicator that this is a dangling record.

### Optional: Use tools to automate this

For a broader overview of available tooling and research, explore:
  
- <a href="https://github.com/topics/subdomain-takeover" target="_blank">Subdomain takeover repo's GitHub</a>

Signature checks are performed by these tools to detect services susceptible to takeovers.

---

## Real world examples of impact

- **Uber (2017)** — Subdomain takeover on `rider.uber.com` due to non-existent CloudFront distribution.  
  <a href="https://hackerone.com/reports/175070" target="_blank">[HackerOne Report #175070]</a>

- **Zenly (Snap Inc.)** — Subdomain takeover of `brand.zen.ly` due to unclaimed external service.  
  <a href="https://hackerone.com/reports/1474784" target="_blank">[HackerOne Report #1474784]</a>

Attackers can serve phishing sites, malicious scripts, or C2 beacons **from a domain the victim trusts**.  
In a supply chain scenario, this could mean **stealing credentials**, **breaking CSP policies**, or hijacking trusted OAuth redirect URIs.

---

## Attack vectors enabled by subdomain takeover

Once an attacker gains control of the subdomain, they can:

- Serve malicious HTML, JS, or phishing pages
- Capture cookies (if misconfigured)
- Abuse `*.example.com` wildcard trust relationships
- Spoof emails (if SPF/DKIM/DMARC are misconfigured)
- Use the domain in OAuth redirect URI attacks
- Host malware while appearing as a trusted domain

---

## How to mitigate subdomain takeovers

The best defense is **prevention through visibility** and **systematic hygiene**:

### ✅ Inventory and monitor

- Maintain a live inventory of **all active subdomains**
- Detect DNS records that point to **external third party platforms**

### ✅ Remove dangling records

- Delete CNAME/A records pointing to services you no longer use
- Clean up test environments during decommissioning

### ✅ Claim and reclaim

- If a subdomain is unused but still resolves — **either remove the DNS record or reclaim the service**

### ✅ Monitor for NXDOMAINs

- Use DNS monitoring tools (e.g., SecurityTrails, DNSDB, PassiveTotal) to detect NXDOMAIN responses on your infrastructure

### ✅ Automate continuous testing

- Run scheduled scans with tools like `amass` to detect changes in DNS resolution and service ownership

---

## Advanced tip: preventive DNS sinkholing

For subdomains you don't plan to use but want to **reserve**, you can redirect them to a sinkhole or dummy service you own. 
This prevents attackers from being able to claim the associated CNAME target.

---

## Resources worth bookmarking

1. <a href="https://www.stratussecurity.com/post/azure-subdomain-takeover-guide" target="_blank">Azure Subdomain Takeover Guide by Stratus Security</a>  
   A comprehensive write-up on how Azure-based subdomain takeovers occur and how to secure against them.

2. <a href="https://github.com/EdOverflow/can-i-take-over-xyz" target="_blank">Can I Take Over XYZ? by EdOverflow</a>  
   A curated list of services known to be vulnerable or safe from takeover when misconfigured. Regularly updated.

3. <a href="https://github.com/indianajson/can-i-take-over-dns" target="_blank">Can I Take Over DNS? by indianajson</a>  
   A list of DNS providers and whether their zones are vulnerable to DNS takeover.

4. <a href="https://godiego.co/posts/STO-AWS/" target="_blank">Subdomain Takeover in AWS: Making a PoC</a>  
   A hands-on walkthrough of subdomain takeover using AWS services.

5. <a href="https://toolbox.googleapps.com/apps/dig/#CNAME/" target="_blank">Google Admin Toolbox: DIG Tool</a>  
   Use this tool to inspect CNAME records in a browser.

---

## TL;DR recap for offensive security practitioners

- Look for dangling CNAMEs pointing to third-party platforms  
- Confirm the target returns `NXDOMAIN` or similar  
- Attempt to claim the target only if **authorized** (e.g., in red teaming or bug bounty)  
- Impact can be **high severity**, especially in OAuth, SSO, or CDN contexts  
- Mitigate by inventorying, monitoring, and cleaning DNS records regularly

---

When it comes to subdomain takeover vulnerabilities, here's the crucial advice: always stay vigilant, automate what you can, and disclose responsibly.
