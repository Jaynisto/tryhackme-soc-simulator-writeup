# 🛡️ tryhackme-soc-simulator-writeup

> Write-up of my TryHackMe SOC Simulator experience: Tier 1 alert triage, log analysis, and incident reporting.

![TryHackMe](https://img.shields.io/badge/TryHackMe-SOC%20Simulator-212C42?logo=tryhackme&logoColor=white)
![Role](https://img.shields.io/badge/Role-SOC%20Tier%201%20Analyst-blue)
![Focus](https://img.shields.io/badge/Focus-Blue%20Team-informational)

---

## 📖 Overview

This repository documents incident response, alert triage, and log correlation scenarios completed in the **TryHackMe SOC Simulator**.

The goal is to demonstrate practical **SOC Tier 1 workflows**:

- Analyzing email gateway, firewall, and proxy logs
- Distinguishing **True Positives** from **False Positives**
- Evaluating threat impact
- Making appropriate **escalation decisions**

---

## 📊 Case Summary

| # | Case | Datasource | Verdict | Escalated? |
|---|------|------------|---------|------------|
| 1 | External Blacklist URL Access Attempt | Firewall Logs | ✅ True Positive | No |
| 2 | HR Onboarding Communication | Email Gateway | ❌ False Positive | No |
| 3 | Amazon Impersonation Phishing (Prevented) | Email Gateway / Firewall | ✅ True Positive | No |
| 4 | Microsoft Typosquatting & Credential Harvester (Allowed) | Email Gateway / Proxy Logs | 🚨 True Positive | **Yes** (Tier 2 / IR) |

---

## 🔍 Case Breakdowns

> **Note:** URLs are defanged (`hxxp`, `[.]`) to prevent accidental clicks.

### Case 1: External Blacklist URL Access Attempt

| Field | Detail |
|-------|--------|
| **Timestamp** | 10/15/2026 11:02:22 UTC |
| **Datasource** | Firewall Logs |
| **Source Host** | `10.20.2.17:34257` |
| **Destination** | `67.199.248.11:80` (`hxxp://bit[.]ly/3sHkX3da12340`) |
| **Action** | Blocked |

**Key Findings**

- Outbound request to a known malicious URL listed in threat intelligence feeds.
- The perimeter firewall successfully blocked the TCP connection.

**Verdict & Escalation**

- **Verdict:** ✅ True Positive
- **Escalation:** No. Prevention controls succeeded and no host compromise occurred.

---

### Case 2: HR Onboarding Communication

| Field | Detail |
|-------|--------|
| **Timestamp** | 10/15/2026 10:57:55 UTC |
| **Datasource** | Email Gateway |
| **Sender** | `onboarding@hrconnex[.]thm` |
| **Recipient** | `j.garcia@thetrydaily[.]thm` |
| **Subject** | Action Required: Finalize Your Onboarding Profile |

**Key Findings**

- Automated heuristics flagged external links sent to new employee Julia Garcia.
- Cross-referencing SIEM ticket logs confirmed `hrconnex[.]thm` is an authorized third-party onboarding platform explicitly expected by HR.

**Verdict & Escalation**

- **Verdict:** ❌ False Positive
- **Escalation:** No. Legitimate business traffic.

---

### Case 3: Amazon Impersonation Phishing (Prevented)

| Field | Detail |
|-------|--------|
| **Timestamp** | 10/15/2026 11:01:08 UTC |
| **Datasource** | Email Gateway / Firewall |
| **Sender** | `urgents@amazon[.]biz` |
| **Recipient** | `h.harris@thetrydaily[.]thm` |
| **Embedded Link** | `hxxp://bit[.]ly/3sHkX3da12340` |

**Key Findings**

- **Phishing indicators:** unofficial sender domain (`amazon[.]biz`), artificial urgency tactic, and a shortened Bitly URL.
- **Log correlation:** the user clicked the link at 11:02:22 UTC (about 1 minute after delivery), triggering the firewall block seen in [Case 1](#case-1-external-blacklist-url-access-attempt).

**Verdict & Escalation**

- **Verdict:** ✅ True Positive
- **Escalation:** No. The outbound connection was intercepted and blocked by the firewall.

---

### Case 4: Microsoft Typosquatting & Credential Harvester (Allowed)

| Field | Detail |
|-------|--------|
| **Timestamp** | 10/15/2026 11:03:26 UTC |
| **Datasource** | Email Gateway / Proxy Logs |
| **Sender** | `no-reply@m1crosoftsupport[.]co` |
| **Recipient** | `c.allen@thetrydaily[.]thm` |
| **Embedded Link** | `hxxps://m1crosoftsupport[.]co/login` |

**Key Findings**

- **Phishing indicators:** typosquatted sender domain (`m1crosoftsupport[.]co`) and a spoofed security alert lure.
- **Proxy log correlation:** the recipient (`10.20.2.25`) clicked the link, and proxy logs confirm the outbound web connection was **ALLOWED**.

**Verdict & Escalation**

- **Verdict:** 🚨 True Positive
- **Escalation:** **YES** (Tier 2 / Incident Response)
- **Impact:** High probability of credential theft / session hijacking.

**Immediate Actions Required**

- [ ] Block `m1crosoftsupport[.]co` on firewall, DNS, and proxy gateways.
- [ ] Isolate endpoint `10.20.2.25` for further triage.

---

## 🧰 Skills Demonstrated

- Alert triage and prioritization
- Email gateway, firewall, and proxy log analysis
- Cross-source log correlation
- Phishing and typosquatting identification
- True Positive / False Positive determination
- Impact assessment and escalation decisions
- Clear, concise incident documentation

---

## ⚠️ Disclaimer

This is an independent write-up of my own work and analysis. TryHackMe and its content, branding, and materials belong to TryHackMe.
