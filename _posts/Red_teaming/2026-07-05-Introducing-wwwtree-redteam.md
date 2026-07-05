---
title: "Introducing wwwtree-redteam"
date: 2026-07-05 10:00:00 +0200
categories: [Red Team, Open Source]
tags: [Red Team, Payload Hosting, Python, PowerShell, Open Source, Offensive Security, Penetration Testing]
pin: false
description: wwwtree-redteam is an enhanced version of wwwtree that automatically generates multiple Windows, Linux, and Python payload delivery commands while hosting an entire directory over HTTP or HTTPS.
image: /assets/img/console-output.png
---

> **wwwtree-redteam** is an enhanced version of the original **wwwtree** that automatically generates multiple payload delivery commands for every hosted file. Instead of manually crafting PowerShell, CertUtil, curl, wget, or Python one-liners, the tool generates them automatically and exposes them through both the terminal and an interactive HTML interface. It is designed to simplify payload hosting and reduce repetitive manual work during red team labs, authorised penetration tests, and other security assessments.
{: .prompt-info }

## Overview

While working through red team labs, I noticed I was repeatedly creating the same payload download and execution commands whenever I needed to host files. Rather than manually rebuilding those commands each time, I decided to extend the original **wwwtree** with functionality that automates the process while remaining equally useful during authorised penetration tests and other security assessments.

The original **wwwtree** is an excellent utility for quickly exposing a directory over HTTP. My goal was not to replace it, but to build upon its solid foundation by automatically generating commonly used payload delivery methods for every hosted file.

The result is **wwwtree-redteam**, an enhanced version of **wwwtree** that automatically generates multiple payload delivery methods for every hosted file.

The tool hosts an entire payload directory over HTTP or HTTPS and automatically generates multiple payload delivery methods for each hosted file. These commands are available directly in the terminal and through an interactive HTML portal, allowing operators to quickly select and copy the most appropriate delivery method for the target environment.

Some of the enhancements over the original project include:

- Automatic payload discovery
- Windows PowerShell commands
- CertUtil and BitsAdmin support
- Base64-encoded PowerShell commands
- Linux `curl` and `wget` commands
- Cross-platform Python commands
- Interactive HTML payload portal
- HTTPS support
- Download logging
- One-click copy buttons

## Console Output

The terminal automatically generates multiple payload delivery methods for every hosted file, allowing operators to quickly copy the most appropriate command.

![Console Output](/assets/img/console-output.png)

## Interactive HTML Payload Portal

The same commands are also available through an interactive HTML interface with one-click copy functionality.

## GitHub Repositories

### wwwtree-redteam (this project)

<a href="https://github.com/asifnawazminhas/wwwtree-redteam" target="_blank" rel="noopener noreferrer">
https://github.com/asifnawazminhas/wwwtree-redteam
</a>

### Original wwwtree

<a href="https://github.com/t3l3machus/wwwtree" target="_blank" rel="noopener noreferrer">
https://github.com/t3l3machus/wwwtree
</a>

## Acknowledgements

This project builds upon the excellent **wwwtree**, created by **Panagiotis Chartas (t3l3machus)**.

Many thanks for creating such a useful utility and making it available to the community. Without the original project, **wwwtree-redteam** would not have been possible.

## Conclusion

Although **wwwtree-redteam** started as a small quality-of-life improvement for my own workflow, I hope others find it equally useful during red team labs, authorised penetration tests, and other security assessments.

If you have ideas for additional payload delivery methods, feature requests, or improvements, feel free to open an issue or submit a pull request on GitHub.

You can find the project here:

<a href="https://github.com/asifnawazminhas/wwwtree-redteam" target="_blank" rel="noopener noreferrer">
https://github.com/asifnawazminhas/wwwtree-redteam
</a>

