# Research Notes — Ethical Hacking Task 02
**Target:** localhost (127.0.0.1)  
**Analyst:** Lakshman Govindan  
**Date:** June 27, 2026  
**OS:** Microsoft Windows 11 24H2 – 25H2

---

## Part A — Nmap Installation & Version

**Command used:**
```
nmap --version
```

**Output:**
```
Nmap version 7.99 ( https://nmap.org )
Platform: i686-pc-windows-windows
Compiled with: nmap-liblua-5.4.8 openssl-3.0.16 nmap-libssh2-1.11.1 nmap-libz-1.3.2 nmap-libpcre2-10.47 Npcap-1.87 nmap-libdnet-1.18.0 ipv6
Available nsock engines: iocp poll select
```

---

## Part B — Basic Localhost Scan

**Command used:**
```
nmap localhost
```

**Output:**
```
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000015s latency).
Not shown: 994 closed tcp ports (reset)
PORT     STATE  SERVICE
135/tcp  open   msrpc
445/tcp  open   microsoft-ds
1434/tcp open   ms-sql-m
5432/tcp open   postgresql
6881/tcp open   bittorrent-tracker
7070/tcp open   realserver
Nmap done: 1 IP address (1 host up) scanned in 0.25 seconds
```

**Findings:**
- Total open ports: 6
- Port 135 — msrpc (Microsoft Remote Procedure Call)
- Port 445 — microsoft-ds (SMB / Windows file sharing)
- Port 1434 — ms-sql-m (Microsoft SQL Server Monitor)
- Port 5432 — postgresql (PostgreSQL Database)
- Port 6881 — bittorrent-tracker (BitTorrent)
- Port 7070 — realserver (RealServer streaming)

---

## Part C — Service Version Detection

**Command used:**
```
nmap -sV localhost
```

**Output:**
```
PORT     STATE  SERVICE       VERSION
135/tcp  open   msrpc         Microsoft Windows RPC
445/tcp  open   microsoft-ds?
1434/tcp open   ms-sql-s      Microsoft SQL Server 2022 16.00.4210
5432/tcp open   postgresql    PostgreSQL DB
6881/tcp open   tcpwrapped
7070/tcp open   ssl/realserver?
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

| Port | Service | Version |
|------|---------|---------|
| 135/tcp | msrpc | Microsoft Windows RPC |
| 445/tcp | microsoft-ds | Not fully detected |
| 1434/tcp | ms-sql-s | Microsoft SQL Server 2022 16.00.4210 |
| 5432/tcp | postgresql | PostgreSQL DB |
| 6881/tcp | tcpwrapped | Not detected (tcpwrapped) |
| 7070/tcp | ssl/realserver | Not fully detected |

---

## Part D — OS Detection

**Command used:**
```
nmap -O localhost
```

**Output:**
```
Device type: general purpose
Running: Microsoft Windows 11
OS CPE: cpe:/o:microsoft:windows_11
OS details: Microsoft Windows 11 24H2 – 25H2
Network Distance: 0 hops
```

**Findings:**
- OS detected: Yes
- OS identified: Microsoft Windows 11 24H2 – 25H2
- Why OS detection is useful: Knowing the target OS helps a penetration tester identify OS-specific vulnerabilities, select appropriate exploits, and understand what default services are likely running.

---

## Part E — Common Port Research

| Port | Protocol | Purpose | Common Security Risks |
|------|----------|---------|----------------------|
| 20/21 | FTP | File Transfer Protocol — transfers files between client and server | Plaintext transmission, anonymous login, brute force attacks |
| 22 | SSH | Secure Shell — encrypted remote login and command execution | Brute force on weak passwords, outdated SSH versions |
| 23 | Telnet | Unencrypted remote login protocol | Everything sent in plaintext including passwords — highly insecure, should never be used |
| 25 | SMTP | Simple Mail Transfer Protocol — sends emails between servers | Open relay abuse for spam, email spoofing without SPF/DKIM |
| 53 | DNS | Domain Name System — resolves domain names to IP addresses | DNS poisoning, DNS amplification DDoS attacks |
| 80 | HTTP | Serves unencrypted web pages | MITM attacks, credential sniffing, no data encryption |
| 110 | POP3 | Post Office Protocol — downloads emails from server to client | Credentials sent in plaintext unless SSL used |
| 143 | IMAP | Internet Message Access Protocol — access emails on server | Plaintext unless IMAPS (993) used — vulnerable to sniffing |
| 443 | HTTPS | HTTP over TLS/SSL — encrypted web traffic | Misconfigured SSL, outdated ciphers, certificate spoofing |
| 445 | SMB | Windows file and printer sharing | EternalBlue/WannaCry ransomware exploit — critical risk if exposed |
| 3389 | RDP | Remote Desktop Protocol — remote graphical access to Windows | Brute force, BlueKeep CVE-2019-0708 — never expose to internet |

---

## Part F — Scan Analysis

**1. Which services are currently running?**
Six services were found running on localhost:
- Port 135 — Microsoft RPC: core Windows inter-process communication
- Port 445 — SMB: Windows file and printer sharing
- Port 1434 — Microsoft SQL Server 2022: database server
- Port 5432 — PostgreSQL: open-source database server
- Port 6881 — BitTorrent tracker: peer-to-peer file sharing service
- Port 7070 — RealServer: media streaming service

**2. Are all open ports necessary?**
Port 135 (RPC) is a Windows core requirement. Port 445 (SMB) is needed only if file sharing is in use. Ports 1434 (SQL Server) and 5432 (PostgreSQL) suggest active database development or applications. Port 6881 (BitTorrent) and 7070 (RealServer) are not standard system services and may have been installed by third-party applications — these should be reviewed and disabled if not actively needed.

**3. Which services could become security risks if misconfigured?**
- Port 445 (SMB) — EternalBlue/WannaCry attack vector, critical risk if exposed to network
- Port 1434 (MS SQL Server) — SQL injection, unauthorized access if not properly secured
- Port 5432 (PostgreSQL) — database exposure, data theft if default credentials are in use
- Port 6881 (BitTorrent) — potential malware distribution, legal and policy risks
- Port 7070 (RealServer) — outdated streaming service, potential unpatched vulnerabilities

**4. Which port would you disable if not required?**
Port 6881 (BitTorrent tracker) — this is not a standard system service and poses both security and policy risks. It should be disabled immediately if not intentionally installed. Port 7070 (RealServer) should also be reviewed and disabled if not in active use.

---

## Part G — Conclusion

The network scanning phase on my local Windows 11 machine using Nmap 7.99 revealed six open ports, significantly more than a default Windows installation would typically show. In addition to the standard Windows services (ports 135 and 445), two database servers were found running — Microsoft SQL Server 2022 (port 1434) and PostgreSQL (port 5432) — indicating active development or application usage. More notably, port 6881 (BitTorrent tracker) and port 7070 (RealServer) were also open, suggesting third-party software installations that may not be necessary. From a security perspective, SMB (445), both database ports, and especially BitTorrent (6881) represent risks that should be addressed by restricting access via firewall rules or disabling the services if not required. This exercise demonstrated how a simple Nmap scan can quickly reveal a machine's complete service footprint and highlight unnecessary or potentially risky open ports.
