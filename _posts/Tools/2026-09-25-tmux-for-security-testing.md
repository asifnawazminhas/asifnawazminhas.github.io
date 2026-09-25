---
title: "tmux for Security Testing: Sessions, Windows, Panes and Long Running Tasks"
date: 2026-09-25 21:50:00 +0200
categories: [Tools]
tags: [tmux, linux, terminal, pentesting, red-teaming, recon, nuclei, ssh, vps]
toc: true
pin: false
description: "A practical tmux reference for penetration testing, reconnaissance, long running scans, remote VPS workflows, panes, windows, sessions and process monitoring."
image:
  path: /assets/img/posts/tmux/tmux.png
  alt: "tmux security testing workflow with multiple terminal panes"
---

## Introduction

![tmux security testing workflow](/assets/img/posts/tmux/tmux.png)

`tmux` is a terminal multiplexer that allows multiple terminal sessions, windows and panes to run inside a single terminal.

It is especially useful during penetration testing, red teaming, CTFs, reconnaissance and remote VPS work because long running processes continue running even when the SSH connection is interrupted.

A typical workflow might look like:

```text
SSH to VPS
   |
   v
tmux session
   |
   +--> Recon
   +--> Scanner
   +--> Logs
   +--> Monitoring
   +--> Shell
```

This makes `tmux` particularly useful for tools such as:

```text
Nuclei
httpx
subfinder
Nmap
ffuf
gobuster
Burp related tooling
custom Python scripts
listeners
C2 infrastructure
```

---

## Installation

On Kali Linux or another Debian based system:

```bash
sudo apt update
sudo apt install -y tmux
```

Verify:

```bash
tmux -V
```

---

## Starting tmux

Start a normal tmux session:

```bash
tmux
```

It is usually better to create a named session:

```bash
tmux new -s recon
```

For example:

```bash
tmux new -s client-recon
```

Named sessions are much easier to identify later.

---

## The tmux Prefix

Most tmux shortcuts start with:

```text
Ctrl+b
```

This is called the tmux prefix.

The sequence is:

```text
Press Ctrl+b
Release the keys
Press the next key
```

For example, to create a new window:

```text
Ctrl+b
c
```

---

# Session Management

## List Sessions

```bash
tmux ls
```

Example:

```text
client-recon: 3 windows
web-scan: 2 windows
monitoring: 1 windows
```

---

## Attach to a Session

```bash
tmux attach -t client-recon
```

Short form:

```bash
tmux a -t client-recon
```

---

## Detach from a Session

Detach without stopping anything:

```text
Ctrl+b
d
```

The commands and processes inside tmux continue running.

Reconnect later:

```bash
tmux attach -t client-recon
```

This is one of the main reasons tmux is useful for remote security testing.

---

## Rename a Session

Inside tmux:

```text
Ctrl+b
$
```

Or from the shell:

```bash
tmux rename-session -t old-session new-session
```

Example:

```bash
tmux rename-session -t recon external-recon
```

---

## Kill a Session

```bash
tmux kill-session -t client-recon
```

Kill all tmux sessions:

```bash
tmux kill-server
```

Be careful with `kill-server` because all tmux sessions and the processes inside them are terminated.

---

# Windows

A tmux session can contain multiple windows.

Windows are similar to tabs in a terminal emulator.

Example:

```text
Session: client-recon

Window 0: enumeration
Window 1: scanner
Window 2: monitoring
Window 3: logs
Window 4: shell
```

---

## Create a Window

```text
Ctrl+b
c
```

---

## Next Window

```text
Ctrl+b
n
```

---

## Previous Window

```text
Ctrl+b
p
```

---

## Jump to a Window

Window 0:

```text
Ctrl+b
0
```

Window 1:

```text
Ctrl+b
1
```

Window 2:

```text
Ctrl+b
2
```

---

## Window Selector

```text
Ctrl+b
w
```

---

## Rename a Window

```text
Ctrl+b
,
```

Useful names include:

```text
recon
nuclei
httpx
logs
monitor
shell
```

---

## Close a Window

Inside the window:

```bash
exit
```

or:

```text
Ctrl+d
```

---

# Panes

A window can be divided into multiple panes.

This is useful when a scanner is running in one pane while monitoring the process from another.

```text
+----------------------------+----------------------------+
|                            |                            |
|         Scanner            |         Monitoring         |
|                            |                            |
|                            |                            |
+----------------------------+----------------------------+
```

---

## Split Vertically

```text
Ctrl+b
%
```

---

## Split Horizontally

```text
Ctrl+b
"
```

---

## Move Between Panes

```text
Ctrl+b
Arrow Key
```

Example:

```text
Ctrl+b
Right Arrow
```

---

## Close a Pane

Inside the pane:

```bash
exit
```

or:

```text
Ctrl+d
```

This closes only the current pane.

You can also use:

```text
Ctrl+b
x
```

---

## Zoom a Pane

```text
Ctrl+b
z
```

Press the same shortcut again to return to the normal layout.

---

## Swap Pane Position

```text
Ctrl+b
{
```

or:

```text
Ctrl+b
}
```

---

## Cycle Pane Layouts

```text
Ctrl+b
Space
```

---

# Scrolling and Copy Mode

Enter copy mode:

```text
Ctrl+b
[
```

Then use:

```text
Arrow keys
Page Up
Page Down
```

Exit:

```text
q
```

---

# Session and Window Navigation

## Session Tree

```text
Ctrl+b
s
```

This opens the session, window and pane tree.

---

## Window Tree

```text
Ctrl+b
w
```

---

# Practical Security Testing Layout

A useful structure for a longer engagement could be:

```text
Session: external-assessment

Window 0: enumeration
Window 1: web-scanning
Window 2: nuclei
Window 3: monitoring
Window 4: logs
Window 5: shell
```

Start it:

```bash
tmux new -s external-assessment
```

Create windows:

```text
Ctrl+b
c
```

Rename them:

```text
Ctrl+b
,
```

---

# Running Long Scans

Example:

```bash
python3 recon.py \
  -d example.com \
  -o example-recon
```

Detach:

```text
Ctrl+b
d
```

Reconnect:

```bash
tmux attach -t external-assessment
```

---

# Monitoring Nuclei

```bash
watch -n 5 'ps -o pid,etime,time,pcpu,pmem,stat,cmd -C nuclei'
```

Example:

```text
PID      ELAPSED     TIME       %CPU  %MEM  STAT  CMD
157704   00:36:09    00:14:38   40.5  4.8   Sl+   nuclei ...
```

| Field | Meaning |
| --- | --- |
| `PID` | Process ID |
| `ELAPSED` | Total elapsed runtime |
| `TIME` | CPU time consumed |
| `%CPU` | Current CPU utilisation |
| `%MEM` | Memory utilisation |
| `STAT` | Process state |
| `CMD` | Command being executed |

Common process states:

```text
R   Running
S   Sleeping or waiting
D   Uninterruptible I/O wait
Z   Zombie process
```

For network based tools, `S` is normal because the process frequently waits for responses.

---

## Monitor Network Connections

```bash
PID=$(pgrep -n nuclei)
```

```bash
sudo ss -tpn | grep "pid=$PID"
```

---

## Monitor Output File Changes

```bash
watch -n 5 'stat -c "%y  %s bytes" nuclei-output.txt'
```

Follow output:

```bash
tail -f nuclei-output.txt
```

An unchanged findings file does not automatically mean that a scanner has stopped.

---

# Multiple Nuclei Processes

Multiple Nuclei processes can run in separate tmux sessions when they belong to different workflows.

Example:

```text
Session: assessment-a
    |
    └── Nuclei workflow A

Session: assessment-b
    |
    └── Nuclei workflow B
```

Check running processes:

```bash
ps -o pid,etime,time,pcpu,pmem,stat,cmd -C nuclei
```

Parallel scanners consume additional:

```text
CPU
RAM
network bandwidth
TCP sockets
file descriptors
```

Avoid multiple processes writing to the same output or state files unless the workflow supports concurrency.

---

# Identify Which tmux Pane Runs a Process

```bash
tmux list-panes -a
```

With process information:

```bash
tmux list-panes -a \
  -F '#S:#I.#P PID=#{pane_pid} CMD=#{pane_current_command}'
```

Example:

```text
external-assessment:0.0 PID=12345 CMD=python3
external-assessment:1.0 PID=12410 CMD=nuclei
monitoring:0.0 PID=12520 CMD=zsh
```

---

# Useful CLI Commands

Create:

```bash
tmux new -s recon
```

List:

```bash
tmux ls
```

Attach:

```bash
tmux attach -t recon
```

Detach a client:

```bash
tmux detach-client -s recon
```

Rename:

```bash
tmux rename-session -t recon external-recon
```

Kill:

```bash
tmux kill-session -t recon
```

List windows:

```bash
tmux list-windows
```

List panes:

```bash
tmux list-panes
```

List all panes:

```bash
tmux list-panes -a
```

---

# Shortcut Reference

| Action | Shortcut |
| --- | --- |
| Prefix | `Ctrl+b` |
| Detach | `Ctrl+b d` |
| New window | `Ctrl+b c` |
| Next window | `Ctrl+b n` |
| Previous window | `Ctrl+b p` |
| Window selector | `Ctrl+b w` |
| Session selector | `Ctrl+b s` |
| Rename window | `Ctrl+b ,` |
| Rename session | `Ctrl+b $` |
| Vertical split | `Ctrl+b %` |
| Horizontal split | `Ctrl+b "` |
| Move between panes | `Ctrl+b Arrow` |
| Close pane | `Ctrl+b x` |
| Zoom pane | `Ctrl+b z` |
| Copy mode | `Ctrl+b [` |
| Cycle layouts | `Ctrl+b Space` |
| Swap pane | `Ctrl+b {` / `Ctrl+b }` |

---

# Quick Workflow

Create a session:

```bash
tmux new -s assessment
```

Split the window:

```text
Ctrl+b
%
```

Run a scanner in one pane.

Run monitoring in the second:

```bash
watch -n 5 'ps -o pid,etime,time,pcpu,pmem,stat,cmd -C nuclei'
```

Zoom:

```text
Ctrl+b
z
```

Detach:

```text
Ctrl+b
d
```

Reconnect:

```bash
tmux attach -t assessment
```

---

## Conclusion

`tmux` is one of the most useful terminal tools for long running security workflows.

Its main advantages are:

```text
persistent processes
multiple terminal windows
split panes
easy monitoring
safe SSH disconnects
clean workflow separation
```

For penetration testing and red teaming, even a basic understanding of sessions, windows, panes and detach/attach functionality can make remote terminal workflows significantly easier to manage.
