# Research Notes — Ethical Hacking Task 01
**Target:** https://www.vit.ac.in/  
**Analyst:** Lakshman Govindan  
**Date:** June 2026

---

## Part B — WHOIS Lookup

**Tool used:** whois.com  
**Domain:** vit.ac.in

### Findings:
- Domain: `vit.ac.in`
- Registered On: 2003-06-30
- Expires On: 2028-06-30
- Updated On: 2019-05-18
- Status: ok
- Registrar: ERNET India (IANA ID: 800068)
- Registrar URL: http://www.ernet.in
- Abuse Email: tejalt@eis.ernet.in
- Abuse Phone: +91.1123358248
- Registrant State: Tamil Nadu
- Registrant Country: IN
- Name Servers:
  - ns-1772.awsdns-29.co.uk
  - ns-389.awsdns-48.com
  - ns-865.awsdns-44.net
  - ns-1067.awsdns-05.org
- DNSSEC: unsigned
- Registrant contact: REDACTED FOR PRIVACY (ICANN compliance)

**Note:** vit.ac.in is a restricted .ac.in domain for Indian academic institutions, managed by ERNET India.

---

## Part C — DNS Enumeration

**Tool used:** dnschecker.org

### A Records:
```
122.184.65.22
```
Single IP — hosted on Tata Communications / Indian ISP infrastructure (not AWS).

### MX Records:
```
1   aspmx.l.google.com
5   alt1.aspmx.l.google.com
5   alt2.aspmx.l.google.com
10  alt3.aspmx.l.google.com
10  alt4.aspmx.l.google.com
```
All MX records point to Google — VIT uses Google Workspace for email.

### NS Records:
```
ns-389.awsdns-48.com
ns-865.awsdns-44.net
ns-1067.awsdns-05.org
ns-1772.awsdns-29.co.uk
```
All four name servers are Amazon Route 53.

### TXT Records:
- SPF: `v=spf1 a:smtp-relay.gmail.com a:ciscoesa.vit.ac.in include:_spf.google.com include:spf.protection.outlook.com include:_spf.vit.ac.in include:amazonses.com -all`
- GlobalSign domain verification token
- Multiple Google site verification tokens
- Escow verification token

---

## Part D — Technology Identification

### From DNS & HTML analysis:
- Hosting: Tata Communications (IP 122.184.65.22 — Indian ISP)
- DNS: Amazon Route 53
- CMS: WordPress (robots.txt reveals /wp-admin/, /xmlrpc.php)
- Email: Google Workspace (all MX records point to Google)
- Email backup: Microsoft 365 (SPF includes spf.protection.outlook.com)
- Email: Amazon SES (SPF includes amazonses.com)
- Email: Cisco ESA (SPF includes ciscoesa.vit.ac.in)
- Verification: GlobalSign SSL certificate
- Verification: Google Search Console (multiple tokens)

### Subdomains (from sitemap/context):
- vit.ac.in — main website
- vitolcc.vit.ac.in — VIT Online Learning (Moodle LMS)

---

## Part E — HTTP Security Headers

**Tool used:** securityheaders.com

| Header | Present | Purpose |
|--------|---------|---------|
| Content-Security-Policy (CSP) | Not Detected | Prevents XSS attacks by specifying which content sources the browser should trust |
| X-Frame-Options | Not Detected | Prevents the page from being embedded in iframes — protects against clickjacking |
| X-Content-Type-Options | Not Detected | Stops browsers from MIME-sniffing a response away from the declared content type |
| Strict-Transport-Security (HSTS) | Not Detected | Forces browsers to always connect via HTTPS even if user types http:// |
| Referrer-Policy | Not Detected | Controls how much referrer information is sent with requests to other pages |
| Permissions-Policy | Not Detected | Controls which browser features (camera, mic, geolocation) the page can access |

HTTPS is active on the site.

---

## Part F — Robots.txt & Sitemap

**robots.txt — PRESENT** at https://www.vit.ac.in/robots.txt

Key findings:
- SemrushBot: Disallow: / (blocked entirely)
- MJ12bot: Disallow: / (blocked entirely)
- GermCrawler: Disallow: / (blocked entirely)
- Sogou: Disallow: / (blocked entirely)
- Google bots: Allow *.js and *.css only
- User-agent *:
  - Disallow: /*?utm_* (blocks UTM tracking URLs)
  - Disallow: /xmlrpc.php (reveals WordPress — common attack vector)
  - Disallow: /admin/ (admin panel path exposed)
  - Disallow: /wp-admin/ (WordPress admin — reveals CMS)
  - Disallow: /comment/reply/
  - Disallow: /search/
  - Disallow: /cgi-bin/
  - Allow: /admissions/overview
  - Allow: /admissions/programmes-offered
  - Allow: /all-courses/ug
  - Allow: /all-courses/pg
  - Disallow: /school/allfaculty/
  - Disallow: /noteable-alumni/
  - Disallow: /category/

**Key finding:** robots.txt reveals /wp-admin/ and /xmlrpc.php — confirming the site uses WordPress CMS. xmlrpc.php is a known WordPress attack vector for brute force and DDoS attacks.

**sitemap.xml — PRESENT** at https://www.vit.ac.in/sitemap.xml

- Generator: Free Online Sitemap Generator (www.xml-sitemaps.com)
- Homepage: https://vit.ac.in/ (priority 1.0)
- Last modified: 2024-11-05
- Indexed pages include: /events-photos-and-videos, /admissions/international/overview/, /about-vit, /about/vision-mission, /vit-milestones, /about/leadership, /governance, /about/administrative-offices, /about/infrastructure, /about/sustainability, /true-green and many more

---

## Other Observations

- WordPress CMS confirmed via /wp-admin/ in robots.txt and URL structure
- xmlrpc.php disallowed — known WordPress XML-RPC attack vector
- Multiple SEO crawlers blocked (SemrushBot, MJ12bot) — site is actively managing crawl budget
- Email infrastructure is complex: Google Workspace + Microsoft 365 + Amazon SES + Cisco ESA
- No CDN detected (single IP, no Cloudflare headers)
- DNSSEC not enabled
