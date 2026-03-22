# 🔐 Web Attack Chain Analysis Report

> A complete professional security incident report documenting **11 real-world web attacks** detected and investigated using **Elastic SIEM**, **Apache access logs**, and **Wireshark packet capture** on an on-premise lab environment.

---

## 📋 Overview

This repository contains a fully documented **Web Attack Chain Analysis Report** covering an end-to-end attack lifecycle against a vulnerable Apache web server. Each attack was detected through **Elastic SIEM** (powered by Filebeat log ingestion) and corroborated with **Wireshark PCAP evidence**, then documented in a professional report format suitable for SOC analysts, incident responders, and security teams.

The report was produced as part of a structured **Blue Team threat detection and investigation exercise** across 11 sequential attack scenarios — from initial reconnaissance through to full server compromise via OS command injection.

---

## 🧱 Environment

| Component | Details |
|-----------|---------|
| **Detection Platform** | Elastic SIEM (Kibana + Filebeat 8.18.0) |
| **Log Source** | Apache HTTP Server access logs |
| **Network Capture** | Wireshark (PCAP analysis) |
| **Target Host** | btweb-virtual-machine |
| **Target IP** | 172.16.25.15 |
| **Primary Attacker IP** | 10.20.10.3 |
| **Secondary Attacker IP** | 10.20.10.4 (Attack 08) |
| **ECS Version** | 1.12.0 |
| **Domain** | On-Premise |

---

## ⚔️ Attack Chain — 11 Attacks Documented

| # | Attack Name | Severity | Detection Source | Key Evidence |
|---|-------------|----------|-----------------|--------------|
| 01 | Login Brute Forcing Activity | 🟠 High | Elastic SIEM + Wireshark | 52 POST failures to `/login.php` from `10.20.10.3` |
| 02 | Subdomain Enumeration Activity | 🟠 High | Elastic SIEM | Wfuzz/3.1.0, massdns, nikto, dirb User-Agent strings |
| 03 | SQL Injection Union Attack | 🔴 Critical | Elastic SIEM + CyberChef | UNION SELECT payloads targeting `information_schema.columns` |
| 04 | SQLMap Attack Detection | 🔴 Critical | Elastic SIEM + CyberChef | 70 events — sqlmap/1.8.5 with BENCHMARK, ROW(), EXP() techniques |
| 05 | Directory Traversal Attack | 🔴 Critical | Elastic SIEM + Wireshark | `../../../../etc/shadow`, `/etc/passwd`, `/etc/mysql/my.cnf` |
| 06 | Classic SQL Injection Detection | 🔴 Critical | Elastic SIEM + Wireshark | `' OR 1=1 #` — 2 confirmed HTTP 200 login bypasses |
| 07 | IDOR Attack | 🟠 High | Elastic SIEM | `user_id=1` and `user_id=3` — unauthorized record access |
| 08 | File Upload Vulnerability Attack | 🔴 Critical | Elastic SIEM + Wireshark | `shell.php` uploaded + executed; TCP RST on port 4444 |
| 09 | XSS Vulnerability Attack | 🟠 High | Elastic SIEM | `prompt`, `onclick`, `script`, `javascript`, `onFocus` patterns |
| 10 | SSRF | 🔴 Critical | Wireshark + CyberChef | `http://127.0.0.1:8080/secret.txt` — 3 repeated internal probes |
| 11 | Command Injection | 🔴 Critical | Elastic SIEM + Wireshark | `ip=127.0.0.1; ls -la /root` — OS RCE via ping form |

**7 Critical · 4 High · 0 Medium/Low**

---

## 📁 Repository Structure

```
web-attack-chain-report/
│
├── README.md                          # This file
│
├── report/
│   ├── web_attack_chain_report.docx   # Full report — Microsoft Word format
│   └── web_attack_chain_report.pdf    # Full report — PDF format
│
└── evidence/
    ├── attack01_elastic.png           # Brute force — Elastic SIEM query
    ├── attack01_wireshark.png         # Brute force — Wireshark PCAP
    ├── attack02_elastic.png           # Enumeration — Wfuzz/3.1.0 events
    ├── attack03_cyberchef.png         # SQLi Union — CyberChef decode
    ├── attack03_elastic.png           # SQLi Union — SIEM events
    ├── attack03_urls.png              # SQLi Union — URL field stats
    ├── attack04_elastic.png           # SQLMap — 70 events
    ├── attack04_cyberchef.png         # SQLMap — decoded payloads
    ├── attack05_elastic.png           # Dir Traversal — SIEM events
    ├── attack05_wireshark.png         # Dir Traversal — PCAP GET requests
    ├── attack06_elastic_fail.png      # Classic SQLi — 18 failure events
    ├── attack06_elastic_success.png   # Classic SQLi — 2 HTTP 200 bypasses
    ├── attack06_wireshark.png         # Classic SQLi — POST stream
    ├── attack06_payload.png           # Classic SQLi — form data dissector
    ├── attack07_elastic.png           # IDOR — user_id=1 and user_id=3
    ├── attack08_elastic1.png          # File Upload — Phase 1 (6 events)
    ├── attack08_elastic2.png          # File Upload — Phase 2 (shell exec)
    ├── attack08_wireshark_shell.png   # File Upload — shell.php GET + 200
    ├── attack08_wireshark_tcp.png     # File Upload — port 4444 TCP RST
    ├── attack09_elastic.png           # XSS — pattern detection events
    ├── attack10_wireshark.png         # SSRF — escalation sequence
    ├── attack10_http_detail.png       # SSRF — HTTP URI dissector
    ├── attack10_cyberchef.png         # SSRF — decoded target URL
    ├── attack11_elastic.png           # Command Injection — 7 POST events
    ├── attack11_ws_post.png           # Command Injection — POST filter
    ├── attack11_ws_form.png           # Command Injection — form filter
    └── attack11_payload.png           # Command Injection — ip field payload
```

---

## 📄 Report Contents

Each of the 11 attack sections contains:

- **Attack Overview Table** — full metadata (IPs, timestamps, endpoint, severity, agent details)
- **Attack Description** — detailed technical narrative explaining the attack mechanics and what occurred
- **Payload / Technique Analysis** — decoded payloads, injection sequences, tool identification
- **Technical Indicators Table** — all observable IOCs and detection artifacts
- **Attack Timeline** — timestamped event sequence from first detection to ingestion
- **Evidence Screenshots** — embedded Elastic SIEM and Wireshark screenshots with figure captions
- **MITRE ATT&CK Mapping** — tactic, technique ID, and technique name per attack
- **Recommendations & Remediation** — 7–8 specific, actionable controls per attack

---

## 🗺️ MITRE ATT&CK Coverage

```
Reconnaissance        T1595, T1595.003, T1590
Initial Access        T1190
Execution             T1059.004, T1059.007
Persistence           T1505.003, T1078.003
Privilege Escalation  T1068
Defense Evasion       T1027, T1078, T1090
Credential Access     T1110, T1110.001, T1539, T1552, T1552.001
Discovery             T1046, T1082, T1083
Lateral Movement      T1599
Collection            T1005, T1185, T1213
Command & Control     T1071.001, T1572
Exfiltration          T1041, T1048
```

---

## 🔍 Attack Chain — Kill Chain Mapping

```
RECONNAISSANCE
└── Attack 02: Subdomain Enumeration (Wfuzz, nikto, massdns, dirb)

INITIAL ACCESS / WEAPONIZATION
├── Attack 01: Login Brute Force → credential discovery
├── Attack 06: Classic SQLi → authentication bypass (OR 1=1)
└── Attack 08: File Upload → web shell deployment (shell.php)

EXPLOITATION
├── Attack 03: SQL Injection UNION → schema & login table dump
├── Attack 04: SQLMap → automated full DB compromise (70 events)
├── Attack 05: Directory Traversal → /etc/shadow, /etc/mysql/my.cnf
├── Attack 09: XSS → client-side script injection
├── Attack 10: SSRF → internal service probe (127.0.0.1:8080/secret.txt)
└── Attack 11: Command Injection → OS RCE (ls -la /root)

POST-EXPLOITATION
├── Attack 07: IDOR → unauthorized user record access
└── Attack 08: Reverse Shell → TCP RST on port 4444 (Metasploit callback)
```

---

## 🛠️ Tools & Technologies Used

### Detection & Investigation
| Tool | Purpose |
|------|---------|
| **Elastic SIEM / Kibana** | Log ingestion, querying, event correlation |
| **Filebeat 8.18.0** | Log shipping from Apache to Elastic |
| **Wireshark** | Network packet capture and deep inspection |
| **CyberChef** | URL decoding of encoded payloads |

### Attack Tools Identified in Evidence
| Tool | Attack | Purpose |
|------|--------|---------|
| **Wfuzz 3.1.0** | Attack 02 | Web content fuzzer |
| **massdns** | Attack 02 | DNS subdomain resolver |
| **nikto** | Attack 02 | Web server vulnerability scanner |
| **dirb** | Attack 02 | Web directory scanner |
| **sqlmap 1.8.5** | Attack 04 | Automated SQL injection framework |
| **Metasploit (inferred)** | Attack 08 | Reverse shell via port 4444 |

---

## 📊 Key Statistics

| Metric | Value |
|--------|-------|
| Total Attacks Documented | 11 |
| Critical Severity | 7 |
| High Severity | 4 |
| Total Evidence Screenshots | 27 |
| Total MITRE Techniques Mapped | 22+ |
| Date Range of Attacks | Apr 25 – May 20, 2025 |
| Primary Attacker IP | 10.20.10.3 |
| Secondary Attacker IP | 10.20.10.4 |
| Target Server | btweb-virtual-machine (172.16.25.15) |
| Highest Event Volume | Attack 04 — SQLMap (70 events) |
| Longest Attack Session | Attack 11 — Command Injection (~2 min, 7 POSTs) |

---

## 📌 Notable Findings

1. **Same attacker throughout** — Agent ID `28533ccf-61c4-47fe-ae31-5b84ccfbc5b4` is consistent across all 11 attacks, confirming a single coordinated threat actor session.

2. **Progressive escalation** — The attack chain follows a clear kill chain: reconnaissance → credential attacks → SQL injection → file access → web shell → RCE. This is not opportunistic — it is a structured, deliberate campaign.

3. **Authentication fully bypassed twice** — Attack 06 confirmed 2 successful HTTP 200 login responses using `' OR 1=1 #` — the attacker gained authenticated access to the application.

4. **Reverse shell attempted** — Attack 08 shows a TCP RST on port 4444 immediately following web shell execution, indicating a Metasploit or netcat reverse shell callback was attempted.

5. **Internal secrets targeted** — Attack 10 (SSRF) made 3 repeated requests for `http://127.0.0.1:8080/secret.txt` — the contents of this file are unknown and must be treated as compromised.

6. **Root directory enumerated** — Attack 11 (`ls -la /root`) was submitted 7 times — full OS command execution was achieved on the web server.

---

## ⚠️ Classification

```
CLASSIFICATION: CONFIDENTIAL
Prepared by: Security Operations Team
Detection Platform: Elastic SIEM
Report Format: DOCX + PDF
```

---

## 📚 References

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [Elastic SIEM Documentation](https://www.elastic.co/guide/en/security/current/index.html)
- [Filebeat Apache Module](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-apache.html)
- [Wireshark Display Filters](https://wiki.wireshark.org/DisplayFilters)
- [SQLMap Project](https://sqlmap.org/)
- [CyberChef](https://gchq.github.io/CyberChef/)

---

## 📝 License

This report and its contents are intended for **educational and defensive security purposes only**. All attack scenarios were conducted in a controlled lab environment. Do not reproduce attack techniques against systems you do not own or have explicit permission to test.

---

*Web Attack Chain Analysis Report · Security Operations Team · March 2026*
