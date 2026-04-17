# 🔐 Brute Force Incident Response Playbook

**Version:** 1.0  
**Author:** Anvesh Raju Vishwaraju  
**MITRE ATT&CK:** T1110 — Brute Force  
**Severity:** Medium → Critical depending on outcome

---

## 🚦 Severity Classification

| Severity | Criteria |
|---|---|
| **Medium** | Failed attempts only, no success, known scanner |
| **High** | 50+ attempts, targeting admin/service accounts |
| **Critical** | Successful login after failures detected |

---

## ⚡ Phase 1 — Detection & Triage (0–10 mins)

### Step 1.1 — Confirm Alert
```bash
# Splunk: Check brute force alert
index=main sourcetype=WinEventLog:Security EventCode=4625
| stats count by src_ip, user, host
| where count > 10
| sort -count

# Wazuh: Check rule 100007 fired
# Dashboard → Security Events → filter rule.id:100007
```

### Step 1.2 — Check for Successful Login
```bash
# CRITICAL CHECK — did attacker get in?
index=main sourcetype=WinEventLog:Security EventCode=4624
| where src_ip="[attacker_ip]"
| table _time, user, src_ip, host, Logon_Type
```

- If **no success** → Medium/High severity → Phase 2
- If **success found** → Critical → Skip to Phase 3 immediately

### Step 1.3 — Identify Source
```bash
# Look up attacker IP
# Tools: VirusTotal, AbuseIPDB, OTX AlienVault
# Questions:
# - Known scanner/bot? (AbuseIPDB score)
# - Targeted (specific users) or random (many users)?
# - Internal or external IP?
```

---

## 🔒 Phase 2 — Containment (10–30 mins)

### For External Brute Force
- [ ] Block `src_ip` at perimeter firewall
- [ ] Add IP to SIEM block list
- [ ] Enable account lockout policy if not already set:
```
GPO: Account Policies → Account Lockout Policy
  Lockout threshold: 5 attempts
  Lockout duration: 30 minutes
  Reset counter: 15 minutes
```

### For Internal Brute Force (Insider / Lateral Movement)
- [ ] Isolate source machine from network
- [ ] Disable source user account in AD
- [ ] Notify IT Manager and CISO immediately
- [ ] Preserve logs before any remediation

### For RDP Brute Force Specifically
```bash
# Move RDP off default port 3389
# Or restrict RDP to VPN only via firewall rule
# Disable RDP on machines that don't need it:
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" -Value 1
```

---

## 🔍 Phase 3 — Critical: Successful Login After Brute Force

> **Treat this as a full compromise. Do not skip steps.**

- [ ] Immediately disable compromised account
- [ ] Revoke all active sessions
- [ ] Isolate affected host from network
- [ ] Preserve memory dump if possible
- [ ] Check what the attacker did post-login:

```bash
# Timeline of actions after successful login
index=main sourcetype=WinEventLog:Security
| where src_ip="[attacker_ip]" AND _time > [successful_login_time]
| table _time, EventCode, user, host, Process_Command_Line
| sort _time
```

- [ ] Check for persistence mechanisms:
  - New user accounts created (EventCode 4720)
  - New scheduled tasks (EventCode 4698)
  - Registry run key changes
  - New services installed (EventCode 7045)

---

## 🧹 Phase 4 — Eradication & Recovery

- [ ] Reset password of targeted account(s)
- [ ] Force MFA enrollment on all affected accounts
- [ ] Audit all accounts that were targeted
- [ ] Patch any vulnerability that enabled the brute force
- [ ] Review and tighten account lockout policy

---

## 📝 Incident Report Template

```
INCIDENT REPORT — Brute Force Attack
Date:
Analyst: Anvesh Raju Vishwaraju
Severity:
Source IP:
Target Accounts:
Total Attempts:
Successful Login: Yes / No

Timeline:
  [HH:MM] First failed attempt detected
  [HH:MM] Alert triggered
  [HH:MM] Investigation started
  [HH:MM] Containment action (IP blocked)
  [HH:MM] Recovery complete

Root Cause:
Actions Taken:
Recommendations:
```

---

## 📞 Escalation Matrix

| Severity | Escalate To | Within |
|---|---|---|
| Medium | Log only | N/A |
| High | SOC Lead | 30 mins |
| Critical (success) | CISO + IT Manager | 15 mins |
