# 🎯 MITRE ATT&CK Coverage Map — SOC Home Lab

**Author:** Anvesh Raju Vishwaraju  
**Framework:** MITRE ATT&CK v14  
**Last Updated:** April 2026

---

## Coverage Summary

| Status | Count |
|---|---|
| ✅ Fully Covered | 12 |
| 🟡 Partial Coverage | 6 |
| ❌ Not Covered | 8 |

---

## Full Coverage Map

### Initial Access
| Technique ID | Technique | Detection Method | Rule File | Status |
|---|---|---|---|---|
| T1566.001 | Spearphishing Attachment | Email gateway + Wazuh | custom-rules.xml | ✅ |
| T1566.002 | Spearphishing Link | Web proxy logs + Suricata | suricata rules | 🟡 |
| T1190 | Exploit Public-Facing App | Suricata IDS signatures | suricata rules | 🟡 |
| T1078 | Valid Accounts | After-hours login rule | after-hours-login.spl | ✅ |

### Execution
| Technique ID | Technique | Detection Method | Rule File | Status |
|---|---|---|---|---|
| T1059.001 | PowerShell | Process creation logs | powershell-abuse.spl | ✅ |
| T1059.003 | Windows Command Shell | EventCode 4688 | custom-rules.xml | ✅ |
| T1204.002 | Malicious File | Wazuh FIM + AV | custom-rules.xml | 🟡 |
| T1053.005 | Scheduled Task | EventCode 4698 | custom-rules.xml | ✅ |

### Persistence
| Technique ID | Technique | Detection Method | Rule File | Status |
|---|---|---|---|---|
| T1136.001 | Create Local Account | EventCode 4720 | custom-rules.xml | ✅ |
| T1098 | Account Manipulation | EventCode 4732 | custom-rules.xml | ✅ |
| T1547.001 | Registry Run Keys | Wazuh FIM on registry | custom-rules.xml | 🟡 |
| T1053.005 | Scheduled Task | EventCode 4698 | custom-rules.xml | ✅ |

### Privilege Escalation
| Technique ID | Technique | Detection Method | Rule File | Status |
|---|---|---|---|---|
| T1068 | Exploit Vulnerability | Suricata + Wazuh | suricata rules | 🟡 |
| T1055 | Process Injection | EventCode 4656 LSASS | custom-rules.xml | ✅ |
| T1548 | Abuse Elevation Control | EventCode 4688 | custom-rules.xml | 🟡 |

### Defense Evasion
| Technique ID | Technique | Detection Method | Rule File | Status |
|---|---|---|---|---|
| T1562.001 | Disable Security Tools | EventCode 5001 | custom-rules.xml | ✅ |
| T1070.001 | Clear Event Logs | EventCode 1102/104 | custom-rules.xml | ✅ |
| T1059.001 | Encoded PowerShell | Process cmd line | powershell-abuse.spl | ✅ |
| T1036 | Masquerading | Process name analysis | custom-rules.xml | ❌ |

### Credential Access
| Technique ID | Technique | Detection Method | Rule File | Status |
|---|---|---|---|---|
| T1110.001 | Brute Force: Password | EventCode 4625 count | brute-force.spl | ✅ |
| T1110.004 | Credential Stuffing | Multiple users, 1 IP | brute-force.spl | ✅ |
| T1003.001 | LSASS Memory | EventCode 4656 | custom-rules.xml | ✅ |
| T1555 | Credentials from Stores | FIM on credential files | custom-rules.xml | ❌ |

### Lateral Movement
| Technique ID | Technique | Detection Method | Rule File | Status |
|---|---|---|---|---|
| T1021.001 | RDP | EventCode 4624 Type 10 | brute-force.spl | ✅ |
| T1021.002 | SMB/Admin Shares | Network logs | suricata rules | 🟡 |
| T1550.002 | Pass the Hash | EventCode 4624 Type 3 | custom-rules.xml | ❌ |

### Exfiltration
| Technique ID | Technique | Detection Method | Rule File | Status |
|---|---|---|---|---|
| T1041 | Exfil Over C2 | Large outbound bytes | data-exfiltration.spl | ✅ |
| T1048.003 | Exfil Over DNS | DNS query volume | data-exfiltration.spl | ✅ |
| T1048 | Exfil Alt Protocol | Unusual port + bytes | data-exfiltration.spl | ✅ |

### Impact
| Technique ID | Technique | Detection Method | Rule File | Status |
|---|---|---|---|---|
| T1486 | Data Encrypted (Ransomware) | FIM bulk changes + YARA | custom-rules.xml | 🟡 |
| T1489 | Service Stop | EventCode 7036 | custom-rules.xml | ❌ |
| T1490 | Inhibit System Recovery | Shadow delete commands | powershell-abuse.spl | ✅ |

---

## ❌ Coverage Gaps — Improvement Roadmap

| Gap | Technique | Plan |
|---|---|---|
| Pass the Hash | T1550.002 | Add Logon Type 3 + NTLM correlation rule |
| Masquerading | T1036 | Add process name vs path mismatch rule |
| Credentials from Stores | T1555 | FIM on browser credential files |
| Service Stop | T1489 | EventCode 7036 alert rule |
| DLL Hijacking | T1574 | Module load monitoring |
| Living off the Land | T1218 | LOLBins watchlist in Splunk |
| Data Staged | T1074 | Large archive creation detection |
| Kerberoasting | T1558.003 | EventCode 4769 spike detection |
