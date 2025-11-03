---
title: Responsible Disclosure Grafana
date: 2025-08-20 07:10:00 +0700
categories: [Responsible_Disclosure]
tags: [Web, Enumeration, Information Gathering, Responsible Disclosure, Grafana, CVE-2025-4123]
pin: false
description: "Summary of my responsible disclosure work reporting open redirect issues (CVE-2025-4123) that I reported"
image: /assets/img/grafana.png
---

## Executive Summary
During a responsible disclosure engagement, an exposed Grafana instance was identified on the target’s infrastructure.  
The version in use was found to be vulnerable to **CVE-2025-4123**, a severity flaw that combines path traversal and open redirect.  
If left unpatched, this issue could allow attackers to redirect users to malicious websites, potentially leading to phishing, data theft, or further exploitation.  

## Impact

Unauthenticated attackers could trick victims into following trusted links that redirect to malicious domains.

The vulnerability was responsibly reported to the affected organisation. 
No data was accessed or modified during testing, and all actions were performed strictly within the boundaries of ethical security research.  

## Enumeration & Introduction
During reconnaissance, the target’s infrastructure was mapped using subdomain enumeration and HTTP probing.  
This process led to the identification of an exposed **Grafana** instance.  

Further testing under responsible disclosure confirmed that the instance was running a version of Grafana vulnerable to **CVE-2025-4123**.  
This vulnerability, already disclosed publicly, enables a **path traversal** that can be exploited for **open redirect**.

If left unpatched, such vulnerabilities can lead to phishing, credential theft, or further exploitation when chained with other flaws.

### Manual Proof of Concept (PoC)

The following request demonstrates a manual **open redirect** exploit:

`http://127.0.0.1:3000/public/..%2F%5cexample.com%2F%3f%2F..%2F..`

When visited, this forces the browser to redirect to `example.com`.

### Example output

`[*] Redirect triggered: http://example.com/`

This confirms the open redirect was successful. It's a key part of the Proof of Concept because it shows the server was forced to redirect the browser 
to the attacker controlled example.com. This proves that the vulnerability works as intended.

### Acknowledgements

* A special thanks to **ynsmorszat** for his work on the public CVE-2025-4123 exploit tool!

### References

* <a href="https://nvd.nist.gov/vuln/detail/CVE-2025-4123" target="_blank">CVE-2025-4123 — NVD</a>  

### Conclusion

The tested Grafana instance was confirmed to be vulnerable to **CVE-2025-4123**. Although the issue had already been disclosed publicly, 
its presence on a production system demonstrates the importance of timely patching. If left unresolved, attackers could potentially escalate from open redirect to SSRF, LFI, or XSS.

**Responsible Disclosure**: This finding was tested for a live site and responsibly reported to the affected organisation through 
their Responsible Disclosure program. 

> Misuse against systems without permission is illegal.
{: .prompt-danger }
