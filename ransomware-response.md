# 🔒 Ransomware Incident Response Playbook

**Version:** 1.0  
**Author:** Anvesh Raju Vishwaraju  
**Severity:** Always CRITICAL  
**Last Updated:** April 2026

---

## ⚠️ IMMEDIATE ACTIONS (First 5 Minutes)

> **DO NOT reboot, pay ransom, or delete files before investigation.**

- [ ] **ISOLATE** affected systems from network immediately
- [ ] **PRESERVE** evidence — do not wipe or restore yet
- [ ] **ALERT** CISO and management immediately
- [ ] **DOCUMENT** everything from this moment forward

---

## 🚨 Phase 1 — Detection (0–10 mins)

### Indicators of Ransomware
- Files renamed with unknown extension (.locked, .encrypted, .WNCRY)
- Ransom note files (README.txt, DECRYPT_INSTRUCTIONS.html)
- High disk I/O with no known process
- Wazuh FIM alerts firing in bulk

### SIEM Query — Detect Mass File Modification
```bash
index=main sourcetype=wazuh_alerts rule.groups="syscheck"
| stats count by agent.name, data.path
| where count > 100
| eval severity="CRITICAL - Possible Ransomware"
| table _time, agent.name, data.path, count, severity
```

---

## 🔒 Phase 2 — Containment (10–30 mins)

- [ ] Pull network cable / disable WiFi on ALL affected hosts
- [ ] Isolate network segment at switch/firewall level
- [ ] Disable affected user accounts in AD
- [ ] Identify patient zero — first infected machine
- [ ] Check for C2 beaconing before isolation

```bash
# Identify C2 communication
index=network sourcetype=firewall_logs
| stats count by src_ip, dest_ip, dest_port
| where count > 100 AND dest_port NOT IN (80,443,53)
| sort -count
```

---

## 🔍 Phase 3 — Investigation (30 mins–4 hours)

- [ ] Identify ransomware variant (upload sample to ID Ransomware)
- [ ] Check if decryptor exists (nomoreransom.org)
- [ ] Determine initial access vector (phishing? RDP? Vulnerability?)
- [ ] Map lateral movement path
- [ ] Identify all affected systems and data

### Timeline Reconstruction
```bash
# Find initial execution event
index=main sourcetype=WinEventLog:Security EventCode=4688
| where _time > [T-2hours before first alert]
| table _time, host, user, New_Process_Name, Process_Command_Line
| sort _time
```

---

## 🧹 Phase 4 — Eradication (4–12 hours)

- [ ] Wipe and rebuild all affected systems from scratch
- [ ] Do NOT restore from backup until malware is fully eradicated
- [ ] Patch vulnerability used for initial access
- [ ] Reset ALL passwords (not just affected users)
- [ ] Rotate service account credentials

---

## 🔄 Phase 5 — Recovery (12–72 hours)

- [ ] Restore from last known clean backup
- [ ] Verify backup integrity before restoring
- [ ] Bring systems back online in isolated environment first
- [ ] Monitor closely for 72 hours post-recovery
- [ ] Confirm business operations are restored

---

## 📝 Phase 6 — Post-Incident (72 hours+)

- [ ] Full incident report for management and regulators
- [ ] CERT-In notification if required (within 6 hours of discovery per IT Act)
- [ ] Lessons learned session with all stakeholders
- [ ] Update IR plan and detection rules
- [ ] Mandatory security awareness training for all staff

---

## ❌ What NOT To Do

| Action | Why |
|---|---|
| Pay the ransom | No guarantee of decryption. Funds criminal activity. |
| Reboot infected machine | May destroy forensic evidence |
| Restore backup to infected network | Will re-infect immediately |
| Panic-delete files | Destroys forensic evidence |
| Announce publicly before containment | Creates panic, tips off attacker |
