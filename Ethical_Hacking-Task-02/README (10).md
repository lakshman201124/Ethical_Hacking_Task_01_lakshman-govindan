# Ethical Hacking Task 01 — Information Gathering & Reconnaissance

**Name:** Lakshman Govindan  
**Internship:** White Band Associates  
**Date:** June 2026  
**Target:** VIT University — https://www.vit.ac.in/

---

## Part A — Target Selection

Chose **VIT University (Vellore Institute of Technology)** as the target — a well-known public Indian deemed university. This is an appropriate ethical target for passive reconnaissance as it is a publicly accessible educational institution website.

---

## Part B — WHOIS Lookup

Performed using `whois.com`.

| Field | Value |
|-------|-------|
| Domain | vit.ac.in |
| Registered On | 2003-06-30 |
| Expires On | 2028-06-30 |
| Last Updated | 2019-05-18 |
| Status | ok |
| Registrar | ERNET India (IANA ID: 800068) |
| Registrant | Tamil Nadu, IN (details redacted) |
| Name Servers | ns-1772.awsdns-29.co.uk, ns-389.awsdns-48.com, ns-865.awsdns-44.net, ns-1067.awsdns-05.org |
| DNSSEC | Unsigned |

---

## Part C — DNS Enumeration

Performed using `dnschecker.org`.

**A Record:**
| IP Address | Notes |
|-----------|-------|
| 122.184.65.22 | Tata Communications — Indian ISP hosting |

**MX Records:** All 5 MX records point to Google (aspmx.l.google.com) — Google Workspace for email.

**NS Records:** All 4 name servers are Amazon Route 53 (awsdns-*).

**TXT Records:** SPF includes Google, Microsoft 365, Amazon SES, Cisco ESA. Multiple Google site verification tokens and GlobalSign domain verification.

---

## Part D — Technology Identification

| Category | Technology | Evidence |
|----------|-----------|---------|
| Hosting | Tata Communications | IP 122.184.65.22 — Indian ISP |
| DNS | Amazon Route 53 | All NS records are awsdns-* |
| CMS | WordPress | /wp-admin/ and /xmlrpc.php in robots.txt |
| Email | Google Workspace | All MX records point to Google |
| Email (backup) | Microsoft 365 | SPF includes spf.protection.outlook.com |
| Email (transactional) | Amazon SES | SPF includes amazonses.com |
| Email (security) | Cisco ESA | SPF includes ciscoesa.vit.ac.in |
| SSL | GlobalSign | GlobalSign domain verification in TXT |
| CDN | None detected | Single IP, no CDN headers |

---

## Part E — HTTP Security Headers

Checked via `securityheaders.com`. HTTPS is active.

| Header | Present | Purpose |
|--------|---------|---------|
| Content-Security-Policy | Not Detected | Prevents XSS by specifying trusted content sources |
| X-Frame-Options | Not Detected | Prevents clickjacking via iframe embedding |
| X-Content-Type-Options | Not Detected | Prevents MIME type sniffing by the browser |
| Strict-Transport-Security (HSTS) | Not Detected | Forces browsers to always use HTTPS |
| Referrer-Policy | Not Detected | Controls referrer info sent with requests |
| Permissions-Policy | Not Detected | Controls browser feature access |

---

## Part F — Robots.txt & Sitemap

**robots.txt** — PRESENT at https://www.vit.ac.in/robots.txt

Notable findings:
- Disallow: /wp-admin/ — **confirms WordPress CMS**
- Disallow: /xmlrpc.php — **known WordPress attack vector**
- Disallow: /admin/, /cgi-bin/, /search/
- Multiple SEO bots (SemrushBot, MJ12bot) blocked entirely
- Google bots allowed only *.js and *.css

**sitemap.xml** — PRESENT at https://www.vit.ac.in/sitemap.xml

- Generator: www.xml-sitemaps.com
- Last modified: 2024-11-05
- Pages include: /about-vit, /about/leadership, /governance, /admissions/, /events-photos-and-videos, /about/infrastructure, /true-green and more

---

## Part G — Conclusion

VIT University's website runs on WordPress CMS hosted on Tata Communications infrastructure with DNS managed by Amazon Route 53. Email is handled by Google Workspace with backup routing through Microsoft 365, Amazon SES, and Cisco ESA — a complex multi-provider email setup. The robots.txt revealed WordPress-specific paths (/wp-admin/, /xmlrpc.php), confirming the CMS and exposing a known attack vector. No HTTP security headers were detected. This reconnaissance showed how passive analysis alone can reveal CMS type, hosting provider, email infrastructure, and sensitive directory paths without touching the target system.

---

## Files

| File | Description |
|------|-------------|
| `Reconnaissance_Report.docx` | Full report with all findings and embedded screenshots |
| `Research_Notes.md` | Raw notes and findings |
| `README.md` | This file |
| `screenshots/` | All 7 screenshots |

---

> **Disclaimer:** All reconnaissance was performed passively using public tools only. No systems were accessed or exploited. Submitted as part of White Band Associates Ethical Hacking Internship Task 01.
