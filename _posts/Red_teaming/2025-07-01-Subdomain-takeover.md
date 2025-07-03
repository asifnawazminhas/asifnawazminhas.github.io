---
title: "Subdomain takeover: A deep dive into a common but overlooked cloud vulnerability"
date: 2025-07-01 10:10:00 +0700
categories: [Red-teaming]
tags: [subdomain takeover, red teaming, DNS, CNAME, cloud security, offensive security]
pin: false
---

> **Disclaimer**  
> This blog post is for **educational and ethical hacking purposes only**. I am **not responsible** for any misuse of the techniques described. 
Always seek **explicit permission** before testing domains or infrastructure that you do not own or operate. Any techniques discussed are intended 
solely for **authorized red teaming, penetration testing**, and **responsible disclosure** efforts.

---

## What is a subdomain takeover?

A **subdomain takeover** occurs when a subdomain (like `staging.example.com`) points via DNS to a service 
that has **not been claimed** or was **deleted but not cleaned up** in DNS.

A subdomain takeover occurs when it points to an external service that no longer exists. This allows an attacker to re-register and control it. 
This is common with cloud providers such as Azure, AWS, etc.

## Why subdomain takeover matters in red teaming and beyond

In red teaming or offensive security simulations, the goal is to think like an attacker - and attackers love low hanging fruit. 
One such vector is the **subdomain takeover**: it’s **stealthy, high-impact, and surprisingly common**, especially in cloud environments.

Subdomain takeovers often don’t require advanced exploit chains. They prey on **poor hygiene**, leftover DNS records, abandoned cloud services 
and lack of visibility. That’s why red teamers, bug bounty hunters, and cloud security engineers alike must understand and monitor for them.

---

## The DNS + CNAME relationship in this attack

**DNS** stands for *Domain Name System*. It translates human readable domain names (like `example.com`) into IP addresses that computers use to identify each other.

In this context, **CNAME records** are a type of DNS record that allow a subdomain to point to another domain name, essentially creating an alias. For example, `blog.example.com` might be a CNAME pointing to `example-blog.hostingplatform.net`.

It becomes a security risk when DNS references (such as CNAME records) persist after the associated cloud service or server is deleted. If the destination (like `example-blog.hostingplatform.net`) no longer exists, an attacker can register that service and take control of the subdomain (`blog.example.com`).

**CNAME** stands for *Canonical Name*. This record can only point to **other domain names**, not directly to **IP addresses**.

A subdomain takeover occurs when a subdomain, which is linked to a service via a CNAME record, is taken over by an attacker. 
This happens because the service provider no longer uses that subdomain, but the CNAME record in the DNS hasn't been removed.


### How subdomain takeovers work

- A subdomain has a `CNAME` record pointing to `orphaned-service.cloudprovider.com`
- The target (`testsubdomaintakeover.azurewebsites.net`) is unclaimed or deleted
- The `CNAME` still exists and resolves
- The attacker registers and takes control of the unclaimed service, then uses the subdomain to serve their content.

![Subdomain Takeover Example](assets/img/subdomain%20takeover%20image.png)

---



---

## How to detect a vulnerable subdomain

To begin, gather a list of subdomains using tools like `amass` or `subfinder`. Then, check if any of them contain `CNAME` records pointing to cloud services. Cross-reference with the status in <a href="https://github.com/EdOverflow/can-i-take-over-xyz" target="_blank">Can I Take Over XYZ</a>. If the status is **Vulnerable** and the fingerprint matches (e.g., NXDOMAIN), the subdomain is likely vulnerable to takeover.

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
dig A poc.azurewebsites.net
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

Attackers can serve phishing sites, malicious scripts, spread malware or use C2 beacons **from a domain the victim trusts**.  
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

### Inventory and monitor

- Maintain a live inventory of **all active subdomains**
- Detect DNS records that point to **external third party platforms**

### Remove dangling records

- Delete CNAME/A records pointing to services you no longer use
- Clean up test environments during decommissioning

### Claim and reclaim

- If a subdomain is unused but still resolves — **either remove the DNS record or reclaim the service**

### Monitor for NXDOMAINs

- Use DNS monitoring tools (e.g., SecurityTrails, DNSDB, PassiveTotal) to detect NXDOMAIN responses on your infrastructure

### Automate continuous testing

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

So, as we wrap up our dive into subdomain takeovers, remember this: in the fast paced world of cloud environments and ever evolving threats, our best defense is to be proactive. Stay alert in monitoring your digital footprint, automate your detection efforts whenever possible to catch those tricky dangling records, and if you uncover a vulnerability, always prioritize responsible disclosure (efforts by the community). By adopting these practices, we can collectively make the internet a safer place, one subdomain at a time.
