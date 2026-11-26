# week4-networkwalks
Pentesting Project

# Mediroza General Hospital — Web Application Penetration Test

A black-box penetration testing engagement conducted as part of the **Networkwalks Cybersecurity Internship (Batch B083, Week 4)**, targeting a purpose-built vulnerable lab environment (`medirozahospital.com`) simulating a hospital's public web application.

> **Disclaimer:** This was an authorized, sandboxed training engagement against a lab environment provided by Networkwalks for educational purposes. No real organization, patient, or individual was targeted. Techniques documented here were performed strictly within the scope and rules of an internship assignment.

---

## Objective

Simulate a real-world black-box pentest engagement: identify exposed entry points, bypass authentication controls, access restricted areas, and uncover sensitive data exposures — then document findings in a professional penetration test report.

## Scope

- Target: `https://medirozahospital.com` (lab environment)
- Approach: Unauthenticated, black-box testing
- No prior credentials, source code, or internal documentation provided

## Methodology

1. **Reconnaissance** — DNS enumeration, port scanning, subdomain/content discovery
2. **Attack surface mapping** — manual browsing, proxy-based traffic analysis
3. **Vulnerability identification** — testing discovered forms and parameters
4. **Exploitation** — confirming impact through controlled proof-of-concept attacks
5. **Post-exploitation analysis** — examining obtained data for secondary leads
6. **Reporting** — full write-up with evidence, risk ratings, and remediation guidance

## Tools Used

| Tool | Purpose |
|---|---|
| `nmap` | Port scanning & service enumeration |
| `gobuster` / `ffuf` | Directory & file brute-forcing |
| `Burp Suite (Community)` | Intercepting proxy, manual request manipulation |
| `sqlmap` | Automated SQL injection testing |
| `John the Ripper` / `hashcat` | Offline password cracking (PDF encryption) |
| `exiftool` / `pdfinfo` | Document metadata analysis |

## Summary of Findings

| # | Finding | Severity |
|---|---|---|
| 1 | SQL injection — authentication bypass on patient login | Critical |
| 2 | Weak/crackable password protection on confidential PDF reports | High |
| 3 | Exposed internal database backup (PII, salary & shareholder data) | Critical |
| 4 | Sensitive information left in document metadata | Medium |
| 5 | Directory listing enabled on multiple web paths | Medium |

**Attack chain:** An unsanitized SQL query in the patient portal login form allowed a classic authentication-bypass payload (`admin'-- -`) to grant unauthorized access without valid credentials. This exposed password-protected PDF lab reports, which were cracked offline using `hashcat`. Metadata inside one decrypted PDF contained an internal note revealing the location of a forgotten legacy database backup, which turned out to be sitting in a publicly browsable, unauthenticated directory — exposing a full HR database.

Full technical detail, proof-of-concept evidence, and remediation recommendations are documented in the complete [penetration test report](#) (shared separately, as it contains full captured evidence).

## Key Takeaways

- Defense-in-depth matters: no single control failing caused this breach — it was a *chain* of smaller weaknesses (unsanitized input → weak encryption → metadata leakage → misconfigured server) compounding into a critical exposure.
- Document metadata is an often-overlooked but genuine attack vector.
- "Security through obscurity" (hidden but guessable/listed directories) is not a substitute for real access control.

## Remediation Highlights

- Parameterized queries / prepared statements for all database interactions
- Strong, randomly generated per-document encryption with secure out-of-band delivery
- Metadata stripping before publishing any external-facing document
- Disable directory listing server-wide; enforce proper authentication on all sensitive paths
- Store backups outside the web root, encrypted at rest, with a defined retention policy

---

*Completed as part of the Networkwalks Online Cybersecurity Internship — Batch B083, Week 4.*
