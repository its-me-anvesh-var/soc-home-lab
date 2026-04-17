# 🎣 Phishing Incident Response Playbook

**Version:** 1.0  
**Author:** Anvesh Raju Vishwaraju  
**Last Updated:** April 2026  
**Severity Levels:** Low / Medium / High / Critical

---

## 📋 Overview

This playbook defines the step-by-step response process for phishing incidents detected via email gateway alerts, user reports, or SIEM correlation rules.

---

## 🚦 Severity Classification

| Severity | Criteria |
|---|---|
| **Low** | Phishing email received, not clicked, no payload |
| **Medium** | Link clicked, no credentials entered, no malware |
| **High** | Credentials entered OR malware downloaded |
| **Critical** | Active compromise, lateral movement detected |

---

## ⚡ Phase 1 — Detection & Triage (0–15 mins)

### Step 1.1 — Confirm the Alert
- [ ] Review email headers (check SPF, DKIM, DMARC)
- [ ] Analyze sender domain (VirusTotal, WHOIS lookup)
- [ ] Check URLs in email (URLScan.io, VirusTotal)
- [ ] Identify all recipients of the phishing email

**Tools:** VirusTotal, MXToolbox, URLScan.io, OTX AlienVault

### Step 1.2 — Determine Click Status
```bash
# Check proxy/firewall logs for URL access
index=network sourcetype=proxy_logs
| search url="*[suspicious-domain]*"
| table _time, src_ip, user, url, action
```

### Step 1.3 — Classify Severity
- No click → **Low** → Proceed to Phase 3
- Click, no creds/malware → **Medium** → Phase 2
- Click + creds/malware → **High/Critical** → Phase 2 immediately

---

## 🔍 Phase 2 — Containment (15–60 mins)

### Step 2.1 — Isolate Affected User (High/Critical only)
- [ ] Disconnect endpoint from network
- [ ] Disable user's Active Directory account
- [ ] Revoke active sessions (Azure AD → Revoke sign-in sessions)
- [ ] Notify user's manager

### Step 2.2 — Block Indicators
- [ ] Block sender domain at email gateway
- [ ] Block malicious URLs at web proxy/firewall
- [ ] Add IOCs to SIEM watchlist
- [ ] Submit to VirusTotal and OTX for community awareness

### Step 2.3 — Check for Lateral Movement
```bash
# Check for logins from compromised account after phishing time
index=main sourcetype=WinEventLog:Security EventCode=4624
    user="[compromised_user]"
| where _time > [phishing_time]
| table _time, user, src_ip, host, Logon_Type
```

---

## 🧹 Phase 3 — Eradication (1–4 hours)

- [ ] Remove phishing email from all mailboxes (admin purge)
- [ ] Scan affected endpoint with AV + EDR
- [ ] Reset compromised credentials
- [ ] Enable MFA if not already active
- [ ] Review email rules (attackers often set auto-forward rules)

```powershell
# Check for suspicious inbox rules (Exchange/M365)
Get-InboxRule -Mailbox "[user@company.com]" | 
  Select Name, Enabled, ForwardTo, RedirectTo, DeleteMessage
```

---

## 🔄 Phase 4 — Recovery (4–24 hours)

- [ ] Restore endpoint from clean backup if malware found
- [ ] Re-enable user account with new credentials + MFA
- [ ] Monitor account for 7 days post-incident
- [ ] Confirm no data exfiltration occurred

---

## 📝 Phase 5 — Post-Incident (24–72 hours)

- [ ] Write incident report (timeline, impact, actions taken)
- [ ] Update SIEM detection rules based on IOCs
- [ ] Run targeted phishing awareness training for affected user
- [ ] Brief management on incident summary

---

## 📊 Incident Report Template

```
INCIDENT REPORT — Phishing
Date: 
Analyst: Anvesh Raju Vishwaraju
Severity: 
Affected Users: 
Timeline:
  - [HH:MM] Phishing email received
  - [HH:MM] Alert triggered / User reported
  - [HH:MM] Investigation started
  - [HH:MM] Containment actions taken
  - [HH:MM] Eradication complete
  - [HH:MM] Recovery complete
IOCs:
  - Sender domain:
  - Malicious URL:
  - File hash (if applicable):
Actions Taken:
Lessons Learned:
Recommendations:
```

---

## 📞 Escalation Matrix

| Severity | Escalate To | Within |
|---|---|---|
| Low | Log only | N/A |
| Medium | SOC Lead | 1 hour |
| High | CISO + IT Manager | 30 mins |
| Critical | CISO + CEO + Legal | 15 mins |
