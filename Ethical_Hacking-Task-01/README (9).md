# Ethical Hacking Task 02 — Network Scanning & Service Enumeration

**Name:** Lakshman Govindan  
**Internship:** White Band Associates  
**Date:** June 27, 2026  
**Target:** localhost (127.0.0.1) — Personal Windows 11 Machine

---

## Part A — Nmap Installation
Installed Nmap 7.99 on Windows 11. Verified using `nmap --version`.

---

## Part B — Basic Scan
Ran `nmap localhost`. Found **6 open ports**:

| Port | Service |
|------|---------|
| 135/tcp | msrpc |
| 445/tcp | microsoft-ds |
| 1434/tcp | ms-sql-m |
| 5432/tcp | postgresql |
| 6881/tcp | bittorrent-tracker |
| 7070/tcp | realserver |

---

## Part C — Service Version Detection
Ran `nmap -sV localhost`.

| Port | Service | Version |
|------|---------|---------|
| 135/tcp | msrpc | Microsoft Windows RPC |
| 445/tcp | microsoft-ds | Not fully detected |
| 1434/tcp | ms-sql-s | Microsoft SQL Server 2022 16.00.4210 |
| 5432/tcp | postgresql | PostgreSQL DB |
| 6881/tcp | tcpwrapped | Not detected |
| 7070/tcp | ssl/realserver | Not fully detected |

---

## Part D — OS Detection
Ran `nmap -O localhost`.

- **OS Detected:** Yes
- **OS:** Microsoft Windows 11 24H2 – 25H2
- **Device Type:** General Purpose
- **Network Distance:** 0 hops

OS detection is useful in penetration testing to identify OS-specific vulnerabilities and select appropriate exploits.

---

## Part E — Common Port Research
Researched 11 common ports: 20/21 (FTP), 22 (SSH), 23 (Telnet), 25 (SMTP), 53 (DNS), 80 (HTTP), 110 (POP3), 143 (IMAP), 443 (HTTPS), 445 (SMB), 3389 (RDP). Full details in `Research_Notes.md`.

---

## Part F — Scan Analysis
- **Running services:** msrpc (135), SMB (445), MS SQL Server 2022 (1434), PostgreSQL (5432), BitTorrent (6881), RealServer (7070)
- **Unnecessary ports:** Port 6881 (BitTorrent) and 7070 (RealServer) are not standard system services and should be reviewed
- **Security risks:** Port 445 (SMB/EternalBlue), Port 1434 (SQL Server — SQL injection risk), Port 5432 (PostgreSQL — data exposure), Port 6881 (BitTorrent — policy and malware risk)
- **Port to disable:** 6881 (BitTorrent) — not a system requirement and poses security and policy risks

---

## Part G — Conclusion
Scanning localhost with Nmap revealed 6 open ports — more than a standard Windows installation. Two database servers (MS SQL Server 2022 and PostgreSQL) indicate active development use. BitTorrent (6881) and RealServer (7070) are third-party services that should be disabled if not in active use. OS was correctly identified as Windows 11 24H2–25H2. This scan highlighted how even a personal machine can have a larger attack surface than expected.

---

## Files
| File | Description |
|------|-------------|
| `Nmap_Scan_Report.docx` | Full professional scan report with embedded screenshots |
| `Research_Notes.md` | Raw notes, commands, findings, and port research |
| `README.md` | This file |
| `screenshots/` | All 4 screenshots from the scan |

---

> **Disclaimer:** All scans were performed only on my own local machine (localhost/127.0.0.1). No external systems were scanned or accessed. Submitted as part of White Band Associates Ethical Hacking Internship Task 02.
