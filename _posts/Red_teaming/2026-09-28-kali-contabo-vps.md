---
title: "Setting Up Kali Linux on a Contabo VPS"
date: 2026-09-28 06:00:00 +0200
categories: [Red-teaming]
tags: [kali linux, contabo, vps, red teaming, penetration testing, xrdp, ssh, offensive security]
pin: false
description: "A practical guide to setting up a Kali Linux penetration testing workstation on a Contabo VPS with SSH, XFCE, xRDP, firewall restrictions, GitHub access, and pentesting tooling."
image: /assets/img/contabo.png
---

# Setting Up Kali Linux on a Contabo VPS

This guide documents how to deploy a fully functional **Kali Linux VPS on Contabo**, including SSH access, the XFCE desktop environment, xRDP for remote desktop access, firewall hardening, and some initial configuration for penetration testing and security research.

The end result is a remotely accessible Kali Linux workstation that can be used for:

- Authorised penetration testing
- Red teaming
- OSEP and other OffSec lab work
- Active Directory testing
- Vulnerability research
- Web application security testing
- Security tooling
- Long-running scans and enumeration

> **Important:** Only perform penetration testing against systems that you own or have explicit permission to test.

---

## Table of Contents

1. [Architecture](#architecture)
2. [Requirements](#requirements)
3. [Install Kali Linux](#install-kali-linux)
4. [Configure Networking](#configure-networking)
5. [Configure the Kali Repository](#configure-the-kali-repository)
6. [Install Kali Linux Packages](#install-kali-linux-packages)
7. [Install XFCE](#install-xfce)
8. [Configure SSH](#configure-ssh)
9. [Connect from Windows using SSH](#connect-from-windows-using-ssh)
10. [Install xRDP](#install-xrdp)
11. [Configure XFCE for xRDP](#configure-xfce-for-xrdp)
12. [Connect from Windows using Remote Desktop](#connect-from-windows-using-remote-desktop)
13. [Configure Clipboard Support](#configure-clipboard-support)
14. [Configure Terminal History](#configure-terminal-history)
15. [Disable Automatic Screen Lock](#disable-automatic-screen-lock)
16. [Find Your Public IP Address](#find-your-public-ip-address)
17. [Configure the Contabo Firewall](#configure-the-contabo-firewall)
18. [Test the Firewall](#test-the-firewall)
19. [Test Outbound Connectivity](#test-outbound-connectivity)
20. [Install Useful Pentesting Dependencies](#install-useful-pentesting-dependencies)
21. [Create a Pentesting Directory Structure](#create-a-pentesting-directory-structure)
22. [Configure GitHub SSH Access](#configure-github-ssh-access)
23. [Clone a Private GitHub Repository](#clone-a-private-github-repository)
24. [Clone Only a Specific Directory](#clone-only-a-specific-directory)
25. [Useful Verification Commands](#useful-verification-commands)
26. [Recommended SSH Hardening](#recommended-ssh-hardening)
27. [Final Architecture](#final-architecture)
28. [Conclusion](#conclusion)

---

# Architecture

The goal is to create the following environment:

```text
┌──────────────────────────────┐
│       Windows Workstation    │
│                              │
│  Windows Terminal            │
│  PowerShell                  │
│  Remote Desktop              │
└──────────────┬───────────────┘
               │
               │ SSH TCP/22
               │ RDP TCP/3389
               │
               ▼
┌──────────────────────────────┐
│       Contabo Firewall       │
│                              │
│ Allow TCP/22 from HOME-IP    │
│ Allow TCP/3389 from HOME-IP  │
│ Block unwanted inbound       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       Contabo Kali VPS       │
│                              │
│ Kali Rolling                 │
│ XFCE                         │
│ OpenSSH                      │
│ xRDP                         │
│ Git                          │
│ Pentesting tools             │
│ Security repositories        │
└──────────────────────────────┘
```

This provides both command-line and graphical access to the Kali VPS.

---

# Requirements

You will need:

- A Contabo VPS
- Access to the Contabo Customer Control Panel
- Kali Linux
- A Windows workstation
- Windows Terminal or PowerShell
- Microsoft Remote Desktop
- Your current public IPv4 address
- A GitHub account if private repositories will be used

A reasonable amount of RAM is recommended if you intend to run applications such as:

- Burp Suite
- Firefox
- BloodHound
- Visual Studio Code
- Metasploit
- Multiple terminals
- Active Directory tooling

---

# Install Kali Linux

Kali Linux can be installed on the Contabo VPS using the installation options available through the Contabo management interface.

If a standard Kali image is unavailable or unsuitable, Kali can also be installed manually using the Kali installer.

During installation, create a normal user such as:

```text
kali
```

The normal user should have `sudo` privileges.

After installation, verify the operating system:

```bash
cat /etc/os-release
```

You can also check the kernel:

```bash
uname -a
```

---

# Configure Networking

When manually installing Kali on a Contabo VPS, you may need to configure the network manually.

Use the network information assigned to your VPS by Contabo.

An example configuration could look like:

```text
IP address: <VPS-IP>/17
Netmask:    255.255.128.0
Gateway:    <VPS-GATEWAY>
DNS:        1.1.1.1 8.8.8.8
```

For example:

```text
Address: 203.0.113.10/17
Gateway: 203.0.128.1
DNS:     1.1.1.1 8.8.8.8
```

> **Important:** Do not copy example IP addresses or gateway values. Use the network configuration assigned to your own VPS.

After installation, verify the network interfaces:

```bash
ip addr
```

Check the routing table:

```bash
ip route
```

A default route should exist through the Contabo gateway.

Test connectivity:

```bash
ping -c 3 1.1.1.1
```

Then test DNS resolution:

```bash
getent hosts kali.org
```

---

# Configure the Kali Repository

If the Kali installer has problems retrieving packages, configure the Kali Rolling repository manually.

Open:

```bash
sudo nano /etc/apt/sources.list
```

Configure:

```text
deb http://kali.download/kali kali-rolling main contrib non-free non-free-firmware
```

Save the file.

Update the package database:

```bash
sudo apt update
```

Upgrade the installed packages:

```bash
sudo apt full-upgrade -y
```

The Kali repository should now be functioning correctly.

---

# Install Kali Linux Packages

Install the standard Kali Linux toolset:

```bash
sudo apt install kali-linux-default -y
```

This provides a useful baseline of penetration-testing tools without installing every package available in Kali.

You can verify installed Kali metapackages with:

```bash
apt list --installed 2>/dev/null | grep kali-linux
```

---

# Install XFCE

For remote graphical access, install the XFCE desktop environment.

```bash
sudo apt install kali-desktop-xfce -y
```

Set the graphical environment as the default boot target:

```bash
sudo systemctl set-default graphical.target
```

Verify:

```bash
systemctl get-default
```

The expected result is:

```text
graphical.target
```

XFCE is generally a good choice for VPS environments because it is relatively lightweight compared with larger desktop environments.

---

# Configure SSH

Install OpenSSH:

```bash
sudo apt install openssh-server -y
```

Enable the SSH service:

```bash
sudo systemctl enable ssh
```

Start SSH:

```bash
sudo systemctl start ssh
```

Verify:

```bash
sudo systemctl status ssh
```

You should see:

```text
active (running)
```

Check whether TCP port 22 is listening:

```bash
sudo ss -tulpn | grep :22
```

You should see the SSH daemon listening on port:

```text
22
```

---

# Connect from Windows using SSH

Open Windows Terminal or PowerShell.

Connect using:

```powershell
ssh kali@<VPS-IP>
```

For example:

```powershell
ssh kali@203.0.113.10
```

The first time you connect, SSH may display:

```text
The authenticity of host ... can't be established.
```

Verify the host information where appropriate and accept the fingerprint.

After authentication, you should receive a Kali shell.

---

# Install xRDP

SSH provides command-line access, but many penetration-testing applications benefit from a graphical environment.

Install xRDP:

```bash
sudo apt install xrdp -y
```

Install additional components required for XFCE sessions:

```bash
sudo apt install dbus-x11 xfce4-session -y
```

Enable xRDP:

```bash
sudo systemctl enable xrdp
```

Start xRDP:

```bash
sudo systemctl start xrdp
```

Verify:

```bash
sudo systemctl status xrdp
```

The service should show:

```text
active (running)
```

Check whether RDP is listening:

```bash
sudo ss -tulpn | grep :3389
```

TCP port `3389` should be listening.

---

# Configure XFCE for xRDP

One common issue with Kali and xRDP is successful authentication followed by an immediate disconnect.

This can happen when the xRDP window manager does not correctly start XFCE.

Back up the existing xRDP startup script:

```bash
sudo cp /etc/xrdp/startwm.sh /etc/xrdp/startwm.sh.bak
```

Edit it:

```bash
sudo nano /etc/xrdp/startwm.sh
```

Use:

```sh
#!/bin/sh

unset DBUS_SESSION_BUS_ADDRESS
unset XDG_RUNTIME_DIR

exec startxfce4
```

Save the file.

Make sure it is executable:

```bash
sudo chmod +x /etc/xrdp/startwm.sh
```

Restart xRDP:

```bash
sudo systemctl restart xrdp
```

Verify:

```bash
sudo systemctl status xrdp
```

---

# Connect from Windows using Remote Desktop

On Windows press:

```text
Win + R
```

Enter:

```text
mstsc
```

Press Enter.

Enter the IP address of the Kali VPS:

```text
<VPS-IP>
```

For example:

```text
203.0.113.10
```

Connect and authenticate using the Kali username and password.

You should now receive the Kali XFCE desktop.

---

# Configure Clipboard Support

xRDP provides clipboard integration through `xrdp-chansrv`.

Check whether the process is running:

```bash
ps aux | grep xrdp-chansrv
```

Check the xRDP configuration:

```bash
grep cliprdr /etc/xrdp/xrdp.ini
```

You should see:

```text
cliprdr=true
```

Inside the Kali terminal, paste using:

```text
Ctrl + Shift + V
```

Copy using:

```text
Ctrl + Shift + C
```

This makes it possible to copy commands between the Windows workstation and the Kali VPS.

---

# Configure Terminal History

When working through long pentesting sessions, retaining terminal scrollback is useful.

Open:

```text
Terminal
→ Preferences
→ General
→ Scrolling
```

Enable:

```text
Unlimited history
```

This prevents older terminal output from disappearing from the terminal scrollback buffer.

---

# Disable Automatic Screen Lock

Automatic screen locking can become inconvenient on a remotely hosted pentesting workstation, particularly during long-running testing sessions.

Disable XFCE session locking:

```bash
xfconf-query -c xfce4-session \
-p /shutdown/LockScreen \
-n -t bool -s false
```

Disable blanking while connected to AC power:

```bash
xfconf-query -c xfce4-power-manager \
-p /xfce4-power-manager/blank-on-ac \
-n -t int -s 0
```

Disable DPMS:

```bash
xfconf-query -c xfce4-power-manager \
-p /xfce4-power-manager/dpms-enabled \
-n -t bool -s false
```

If XFCE Screensaver is installed, disable the screensaver:

```bash
xfconf-query -c xfce4-screensaver \
-p /saver/enabled \
-n -t bool -s false
```

Disable screensaver locking:

```bash
xfconf-query -c xfce4-screensaver \
-p /lock/enabled \
-n -t bool -s false
```

---

# Find Your Public IP Address

Before restricting the Contabo firewall, determine the public IP address of the Windows workstation or network from which you will connect.

Open Windows PowerShell:

```powershell
(Invoke-RestMethod -Uri "https://api.ipify.org")
```

The result might look like:

```text
203.0.113.25
```

To represent a single IPv4 address in CIDR notation:

```text
203.0.113.25/32
```

The `/32` means that only this individual IPv4 address is included.

This is different from your local Windows address.

To view local network addresses:

```powershell
ipconfig
```

Look for:

```text
IPv4 Address
```

For the Contabo firewall source rule, you normally want your **public IP address**, not the private LAN address.

---

# Configure the Contabo Firewall

Exposing SSH and RDP directly to the entire Internet is unnecessary when you normally connect from a known public IP address.

Create a firewall through the Contabo Customer Control Panel.

Configure an SSH rule:

```text
Protocol: TCP
Port:     22
Source:   <YOUR-PUBLIC-IP>/32
Action:   ACCEPT
```

Configure an RDP rule:

```text
Protocol: TCP
Port:     3389
Source:   <YOUR-PUBLIC-IP>/32
Action:   ACCEPT
```

For example:

```text
TCP    22      203.0.113.25/32    ACCEPT
TCP    3389    203.0.113.25/32    ACCEPT
```

Then configure the firewall so that unwanted inbound connections are blocked.

The resulting policy should conceptually look like:

```text
Inbound

TCP 22      HOME-PUBLIC-IP/32    ACCEPT
TCP 3389    HOME-PUBLIC-IP/32    ACCEPT
All other inbound traffic         BLOCK
```

This significantly reduces exposure of SSH and RDP.

> **Warning:** Incorrect firewall rules can lock you out of your VPS. Always keep an existing SSH session open while testing new firewall rules.

---

# Test the Firewall

Do not immediately disconnect your existing SSH session after applying the firewall.

Keep the existing session open.

Open a second Windows Terminal window.

Test SSH:

```powershell
ssh kali@<VPS-IP>
```

If the second connection works, SSH is accessible through the firewall.

Now test Remote Desktop:

```text
Win + R
```

Enter:

```text
mstsc
```

Connect to:

```text
<VPS-IP>
```

Confirm that RDP also works.

Only close your original administrative session after confirming that both SSH and RDP are functioning correctly.

---

# Test Outbound Connectivity

The Kali VPS needs outbound Internet access for:

- Kali repositories
- GitHub
- DNS
- Go modules
- Python packages
- Security tools
- Updates
- External APIs used during authorised assessments

Test direct connectivity:

```bash
ping -c 3 1.1.1.1
```

Test DNS:

```bash
getent hosts github.com
```

Test HTTPS:

```bash
curl -I https://github.com
```

Test Kali repositories:

```bash
sudo apt update
```

If these commands succeed, outbound connectivity is working.

---

# Install Useful Pentesting Dependencies

Install some useful baseline utilities:

```bash
sudo apt install -y \
git \
curl \
wget \
python3 \
python3-pip \
python3-venv \
golang \
build-essential \
jq \
tmux \
net-tools \
dnsutils
```

These packages provide a good foundation for installing additional offensive security tools.

---

## Active Directory Tools

For authorised Active Directory assessments, useful tools include:

```bash
sudo apt install -y \
ldap-utils \
ldapdomaindump \
smbclient \
smbmap \
impacket-scripts \
certipy-ad \
hashcat \
john \
dnsrecon \
dnsenum
```

Additional tools commonly used for AD security testing include:

- NetExec
- BloodHound CE
- RustHound CE
- Certipy
- Impacket
- bloodyAD
- ldeep
- windapsearch
- Kerbrute
- Responder
- mitm6
- enum4linux-ng
- Coercer
- krbrelayx
- PKINITtools
- Ligolo-ng

Install these tools as required rather than installing everything automatically.

---

# Create a Pentesting Directory Structure

It is useful to keep tools and engagement data separated.

Create:

```bash
mkdir -p ~/pentesting
mkdir -p ~/tools
mkdir -p ~/engagements
mkdir -p ~/wordlists
```

The home directory could then look like:

```text
/home/kali/
├── engagements/
├── pentesting/
├── tools/
└── wordlists/
```

For individual engagements:

```bash
mkdir -p ~/engagements/example.com/{recon,scans,evidence,screenshots,notes,reports}
```

Result:

```text
engagements/
└── example.com/
    ├── recon/
    ├── scans/
    ├── evidence/
    ├── screenshots/
    ├── notes/
    └── reports/
```

This provides a consistent structure for keeping engagement information organised.

---

# Configure GitHub SSH Access

Private security repositories can be accessed from the VPS using SSH keys.

Generate a dedicated GitHub SSH key:

```bash
ssh-keygen -t ed25519 \
-C "github-kali-vps" \
-f ~/.ssh/github_kali_vps
```

The following files will be created:

```text
~/.ssh/github_kali_vps
~/.ssh/github_kali_vps.pub
```

The private key is:

```text
github_kali_vps
```

The public key is:

```text
github_kali_vps.pub
```

Never upload or share the private key.

Display the public key:

```bash
cat ~/.ssh/github_kali_vps.pub
```

Copy the output.

Add it to your GitHub account under the SSH key settings.

After adding the key, test authentication:

```bash
ssh -i ~/.ssh/github_kali_vps \
-o IdentitiesOnly=yes \
-T git@github.com
```

Successful authentication should return a message similar to:

```text
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

This means SSH authentication is working correctly.

---

# Clone a Private GitHub Repository

To clone a private repository using the dedicated key:

```bash
GIT_SSH_COMMAND="ssh -i /home/kali/.ssh/github_kali_vps -o IdentitiesOnly=yes" \
git clone git@github.com:<username>/<repository>.git
```

For example:

```bash
GIT_SSH_COMMAND="ssh -i /home/kali/.ssh/github_kali_vps -o IdentitiesOnly=yes" \
git clone git@github.com:username/security-tools.git
```

---

## Configure the SSH Key Permanently for the Repository

Using `GIT_SSH_COMMAND` only affects that individual command.

If the repository is a partial clone or future commands such as `git pull` need authentication, configure the SSH command for that repository.

Enter the repository:

```bash
cd ~/security-tools
```

Configure:

```bash
git config core.sshCommand "ssh -i /home/kali/.ssh/github_kali_vps -o IdentitiesOnly=yes"
```

Verify:

```bash
git config --get core.sshCommand
```

The result should resemble:

```text
ssh -i /home/kali/.ssh/github_kali_vps -o IdentitiesOnly=yes
```

Future commands such as:

```bash
git pull
```

and:

```bash
git fetch
```

will now use the correct SSH key.

---

# Clone Only a Specific Directory

Git does not directly clone an individual directory from a repository.

However, **sparse checkout** can be used so that only a selected directory appears in the working tree.

Clone without checking files out:

```bash
GIT_SSH_COMMAND="ssh -i /home/kali/.ssh/github_kali_vps -o IdentitiesOnly=yes" \
git clone \
--filter=blob:none \
--no-checkout \
git@github.com:<username>/<repository>.git \
~/repository
```

Enter the repository:

```bash
cd ~/repository
```

Configure the SSH key permanently:

```bash
git config core.sshCommand "ssh -i /home/kali/.ssh/github_kali_vps -o IdentitiesOnly=yes"
```

Initialise sparse checkout:

```bash
git sparse-checkout init --cone
```

Select the required directory:

```bash
git sparse-checkout set allscriptsusedinlabs
```

Check out the main branch:

```bash
git checkout main
```

Verify:

```bash
ls -la
```

Then:

```bash
ls -la allscriptsusedinlabs
```

Only the selected directory should be present in the working tree.

---

## Copy the Directory Outside the Git Repository

If Git integration is no longer required and you simply want the files on the VPS, copy the directory outside the repository.

For example:

```bash
cp -a ~/repository/allscriptsusedinlabs ~/allscriptsusedinlabs
```

Verify:

```bash
ls -la ~/allscriptsusedinlabs
```

Once you have confirmed that everything is present, remove the cloned repository:

```bash
rm -rf ~/repository
```

The standalone directory remains:

```text
/home/kali/allscriptsusedinlabs/
```

Removing the local clone does **not** delete anything from GitHub.

---

# GitHub SSH Permission Troubleshooting

If Git returns:

```text
Load key "/home/kali/.ssh/github_kali_vps": Permission denied
```

check ownership:

```bash
ls -la ~/.ssh/
```

The SSH key should belong to the Kali user.

Fix ownership if necessary:

```bash
sudo chown kali:kali /home/kali/.ssh/github_kali_vps
sudo chown kali:kali /home/kali/.ssh/github_kali_vps.pub
```

Configure private-key permissions:

```bash
chmod 600 /home/kali/.ssh/github_kali_vps
```

Configure public-key permissions:

```bash
chmod 644 /home/kali/.ssh/github_kali_vps.pub
```

Verify:

```bash
ls -l /home/kali/.ssh/github_kali_vps*
```

Expected:

```text
-rw------- 1 kali kali ... github_kali_vps
-rw-r--r-- 1 kali kali ... github_kali_vps.pub
```

Test again:

```bash
ssh -i /home/kali/.ssh/github_kali_vps \
-o IdentitiesOnly=yes \
-T git@github.com
```

---

## Avoid Using sudo for Git Operations

Do not normally clone repositories into your Kali home directory using:

```bash
sudo git clone ...
```

Running Git through `sudo` can cause:

- Files owned by root
- SSH key permission problems
- Different SSH configuration
- Different `known_hosts` files
- `~` resolving unexpectedly
- Git authentication problems

For repositories under:

```text
/home/kali/
```

run Git as the normal `kali` user.

---

# Useful Verification Commands

## Network Interfaces

```bash
ip addr
```

---

## Routing

```bash
ip route
```

---

## DNS Configuration

```bash
cat /etc/resolv.conf
```

---

## Internet Connectivity

```bash
ping -c 3 1.1.1.1
```

---

## DNS Resolution

```bash
getent hosts github.com
```

---

## HTTPS Connectivity

```bash
curl -I https://github.com
```

---

## Listening Ports

```bash
sudo ss -tulpn
```

---

## SSH Status

```bash
systemctl status ssh
```

---

## xRDP Status

```bash
systemctl status xrdp
```

---

## SSH Listening Port

```bash
sudo ss -tulpn | grep :22
```

---

## RDP Listening Port

```bash
sudo ss -tulpn | grep :3389
```

---

## Disk Usage

```bash
df -h
```

---

## Memory

```bash
free -h
```

---

## CPU Information

```bash
lscpu
```

---

## Kali Version

```bash
cat /etc/os-release
```

---

## Kernel

```bash
uname -a
```

---

## System Uptime

```bash
uptime
```

---

# Recommended SSH Hardening

Once the VPS is working correctly, SSH should be hardened.

A good next step is to use SSH public-key authentication instead of relying on passwords.

Generate an SSH key on the Windows workstation if one does not already exist:

```powershell
ssh-keygen -t ed25519
```

The public key can then be added to:

```text
/home/kali/.ssh/authorized_keys
```

Test key-based authentication **before disabling password authentication**.

After confirming that SSH key authentication works, edit:

```bash
sudo nano /etc/ssh/sshd_config
```

Consider configuring:

```text
PermitRootLogin no
PubkeyAuthentication yes
PasswordAuthentication no
```

Before restarting SSH, validate the configuration:

```bash
sudo sshd -t
```

If no error is returned:

```bash
sudo systemctl restart ssh
```

Keep the existing SSH session open.

Open another Windows Terminal and verify that key authentication works:

```powershell
ssh kali@<VPS-IP>
```

Only close the original session after confirming that the new connection succeeds.

---

# Security Considerations

A VPS used for penetration testing is still an Internet-facing server and should be treated accordingly.

Recommended practices include:

- Restrict SSH to trusted source addresses
- Restrict RDP to trusted source addresses
- Use SSH public-key authentication
- Disable direct root SSH login
- Disable SSH password authentication after testing keys
- Keep Kali updated
- Avoid exposing unnecessary services
- Regularly check listening ports
- Protect private GitHub SSH keys
- Keep client engagement data separated
- Remove engagement data when no longer required
- Use unique credentials for the VPS
- Monitor disk usage during large scans
- Take snapshots before major system changes where appropriate

Regularly inspect listening services:

```bash
sudo ss -tulpn
```

If a service does not need to be accessible externally, do not expose it through the Contabo firewall.

---

# Updating Kali

Update the package database:

```bash
sudo apt update
```

Upgrade packages:

```bash
sudo apt full-upgrade -y
```

Remove packages that are no longer required:

```bash
sudo apt autoremove -y
```

Reboot when required:

```bash
sudo reboot
```

After rebooting, reconnect:

```powershell
ssh kali@<VPS-IP>
```

---

# Troubleshooting xRDP

If Remote Desktop authenticates successfully but immediately disconnects, check:

```bash
sudo systemctl status xrdp
```

Check the xRDP logs:

```bash
sudo journalctl -u xrdp --no-pager -n 100
```

Check the session manager:

```bash
sudo journalctl -u xrdp-sesman --no-pager -n 100
```

Verify:

```bash
cat /etc/xrdp/startwm.sh
```

It should contain:

```sh
#!/bin/sh

unset DBUS_SESSION_BUS_ADDRESS
unset XDG_RUNTIME_DIR

exec startxfce4
```

Ensure the required packages exist:

```bash
sudo apt install dbus-x11 xfce4-session -y
```

Restart xRDP:

```bash
sudo systemctl restart xrdp
```

Then reconnect using Windows Remote Desktop.

---

# Troubleshooting SSH

If SSH is not working, check:

```bash
sudo systemctl status ssh
```

Verify TCP port 22:

```bash
sudo ss -tulpn | grep :22
```

Check the SSH configuration:

```bash
sudo sshd -t
```

Check recent SSH logs:

```bash
sudo journalctl -u ssh --no-pager -n 100
```

Also verify that the Contabo firewall allows:

```text
TCP/22
```

from your current public IP address.

Remember that residential public IP addresses can change.

You can check the current public IP from Windows again using:

```powershell
(Invoke-RestMethod -Uri "https://api.ipify.org")
```

If the address has changed, update the Contabo firewall rule accordingly.

---

# Final Architecture

After completing the setup, the environment looks approximately like this:

```text
                    Internet
                       │
                       │
             ┌─────────▼─────────┐
             │ Contabo Firewall  │
             │                   │
             │ TCP/22            │
             │ TCP/3389          │
             │ trusted IP only   │
             └─────────┬─────────┘
                       │
                       │
             ┌─────────▼─────────┐
             │   Kali Linux VPS  │
             │                   │
             │ Kali Rolling      │
             │ XFCE              │
             │ OpenSSH           │
             │ xRDP              │
             │ Git               │
             │ Pentest tools     │
             └──────┬───────┬────┘
                    │       │
             HTTPS/DNS      │ Git SSH
                    │       │
                    ▼       ▼
             Kali repos   GitHub
                    ▲
                    │
                    │
        ┌───────────┴────────────┐
        │   Windows Workstation  │
        │                        │
        │ Windows Terminal       │
        │ PowerShell             │
        │ Remote Desktop         │
        └────────────────────────┘
```

The Windows workstation connects to the VPS using:

```text
SSH
Windows → Contabo Firewall → TCP/22 → Kali VPS
```

and:

```text
Remote Desktop
Windows → Contabo Firewall → TCP/3389 → Kali XFCE
```

The Kali VPS can make outbound connections for:

```text
APT
GitHub
DNS
HTTPS
Go packages
Python packages
Security tooling
```

---

# Final Result

After completing this setup, the Contabo VPS provides a remotely accessible Kali Linux penetration-testing workstation with:

- Kali Linux Rolling
- Kali's standard penetration-testing toolset
- XFCE graphical desktop
- SSH command-line access
- xRDP graphical access
- Windows Remote Desktop compatibility
- Clipboard integration
- Persistent terminal scrollback
- Restricted SSH exposure
- Restricted RDP exposure
- Contabo firewall protection
- Outbound Internet connectivity
- GitHub SSH integration
- Private repository support
- A structured pentesting workspace
- Support for Active Directory tooling
- Support for web application testing
- Support for OSEP and other security lab environments

The VPS can now act as a persistent remote penetration-testing workstation without requiring the local Kali machine to remain online.

---

## Quick Reference

### SSH into Kali

```powershell
ssh kali@<VPS-IP>
```

### Open Remote Desktop

```text
mstsc
```

### Update Kali

```bash
sudo apt update && sudo apt full-upgrade -y
```

### Check SSH

```bash
systemctl status ssh
```

### Check xRDP

```bash
systemctl status xrdp
```

### Check listening ports

```bash
sudo ss -tulpn
```

### Check public IP from Windows

```powershell
(Invoke-RestMethod -Uri "https://api.ipify.org")
```

### Test Internet access

```bash
ping -c 3 1.1.1.1
```

### Test DNS

```bash
getent hosts github.com
```

### Test HTTPS

```bash
curl -I https://github.com
```

### Check disk space

```bash
df -h
```

### Check memory

```bash
free -h
```

---

## Disclaimer

This environment is intended for **authorised penetration testing, security research, training, and lab environments**.

Only test systems for which you have explicit authorisation. The VPS owner is responsible for ensuring that all testing complies with the applicable rules of engagement, service-provider policies, and relevant laws.
