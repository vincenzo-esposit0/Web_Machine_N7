<h1 align="center">Web Machine (N7): Penetration Test</h1>

<p align="center">
  <b>End-to-end grey-box penetration test of a vulnerable-by-design web server, with a full report and remediation plan.</b><br>
  Penetration Testing and Ethical Hacking · MSc, University of Salerno · A.Y. 2022/23
</p>

<p align="center">
  <a href="PT_Report_VincenzoEsposito.pdf"><b>📄 Report (33 pp.)</b></a> ·
  <a href="PT_Metodologia_VincenzoEsposito.pdf"><b>🧭 Methodology (66 pp.)</b></a> ·
  <a href="Report/VulnerabilityMapping"><b>📊 Scanner output</b></a>
</p>

> [!NOTE]
> Academic project. The target, [Web Machine: (N7)](https://www.vulnhub.com/entry/web-machine-n7,756/) from VulnHub, is an intentionally vulnerable virtual machine, tested only inside an isolated lab on my own computer. This repository presents the project and the skills it shows. It is not a guide for testing systems without authorisation.

---

## At a glance

| | |
|---|---|
| **My role** | Sole penetration tester: planning, execution, analysis and reporting |
| **Approach** | Grey box. Only the target's operating system was known; its IP address and services were not |
| **Lab** | Kali Linux attacker and N7 target on an isolated virtual network (UTM, NAT) |
| **Outcome** | Flag captured and application database compromised. Full remote control was not achieved, and the report documents why |
| **Findings** | Nessus: 9 critical, 2 high · OWASP ZAP: 2 medium, 4 low · plus informational items |
| **Deliverables** | Executive report with a prioritised remediation plan, a 66-page methodology and raw scanner output |

## My contribution

I ran the whole engagement on my own, following a structured, PTES-style process:

- **Planned the test:** defined the lab, the scope and the grey-box rules of engagement.
- **Mapped the attack surface:**
  - discovered the host and fingerprinted its operating system, both passively and actively;
  - ran full TCP and UDP port scans;
  - enumerated web content and uncovered two endpoints that ordinary browsing does not show.
- **Correlated results across tools:** compared manual CVE research with Nessus (two scan policies) and OWASP ZAP, which separated real issues from scanner noise.
- **Chained the vulnerabilities:** combined a CSRF weakness, a client-side role cookie and an SQL injection to capture the flag and reach the application database.
- **Documented negative results:** recorded every remote-code-execution attempt that failed and explained why. That is how a real report keeps the client's expectations accurate.
- **Wrote both reports:** an executive-level report with findings, severity and remediation, and a reproducible technical methodology.

## Process

| Phase | What I did | Key result |
|---|---|---|
| 1 · Information gathering | Host discovery and OS fingerprinting (passive and active) | Target identified: Linux, Apache 2.x |
| 2 · Enumeration | Full TCP/UDP port scans, web content discovery | One exposed service, Apache httpd 2.4.46 (Debian); hidden endpoints found |
| 3 · Vulnerability mapping | Manual CVE research, Nessus, OWASP ZAP | Outdated web server, injection and CSRF weaknesses, missing hardening |
| 4 · Exploitation | Verified the most promising weaknesses in a controlled way | Flag captured, database access, admin credentials recovered |
| 5 · Post-exploitation | Attempted to escalate to remote control | Not achieved in this configuration; documented |
| 6 · Reporting | Severity rating, impact analysis, remediation plan | [Report](PT_Report_VincenzoEsposito.pdf) and [methodology](PT_Metodologia_VincenzoEsposito.pdf) |

## Key findings

| Severity | Finding | Impact |
|---|---|---|
| 🔴 Critical | **Outdated Apache httpd 2.4.46.** Several known CVEs, including SSRF, request smuggling and memory-safety bugs (e.g. CVE-2021-40438, CVE-2022-22720, CVE-2021-44790) | Broad exposure to publicly known vulnerabilities |
| 🟠 High | **SQL injection** in the login form | Read access to the application database and its credentials |
| 🟡 Medium | **Missing anti-CSRF protection.** A leftover proof-of-concept page remained on the server | Unwanted actions on behalf of a user; information disclosure |
| 🟡 Medium | **User role stored on the client** in a weakly encoded cookie | Role disclosure; authorisation decisions the client can influence |
| 🟡 Medium | **Missing CSP and anti-clickjacking headers** | Larger impact from XSS and clickjacking |
| 🟢 Low | **Server version disclosure** and missing `X-Content-Type-Options` | Easier reconnaissance for an attacker |

The full list, with descriptions, CVE references and solutions, is in chapter 6 of the [report](PT_Report_VincenzoEsposito.pdf).

## Remediation plan

1. **Patch:** upgrade Apache httpd to a supported release and apply regular updates to every exposed service.
2. **Fix the injection:** use parameterised queries, validate input on the server, and give the database user only the privileges it needs.
3. **Protect state-changing requests:** use anti-CSRF tokens and make authorisation decisions on the server, never from client-side cookies.
4. **Harden HTTP:** add CSP, `X-Frame-Options`/`frame-ancestors` and `X-Content-Type-Options`, and hide the server version.
5. **Development hygiene:** remove test pages and debugging artefacts before deployment.
6. **Test continuously:** schedule periodic security assessments.

## Toolkit

`Kali Linux` · `Nmap` (incl. Vulscan) · `netdiscover` · `p0f` · `Unicornscan` · `dirb` / `DirBuster` · `Gobuster` · `Nessus` · `OWASP ZAP` · `Burp Suite` · `sqlmap` · `Metasploit` / `Armitage`

## Repository contents

| Path | Contents |
|---|---|
| [`PT_Report_VincenzoEsposito.pdf`](PT_Report_VincenzoEsposito.pdf) | Executive summary, findings, severity, remediation |
| [`PT_Metodologia_VincenzoEsposito.pdf`](PT_Metodologia_VincenzoEsposito.pdf) | Phase-by-phase technical methodology |
| [`Report/VulnerabilityMapping/`](Report/VulnerabilityMapping) | Raw Nessus, Vulscan and DirBuster output |
| [`Utility/`](Utility) | Lab artefacts referenced in the methodology |

Documents are in Italian.

---

<sub>Vincenzo Esposito · <a href="https://vincenzo-esposit0.github.io/">Website</a> · <a href="https://www.linkedin.com/in/vincenzo-e">LinkedIn</a></sub>
