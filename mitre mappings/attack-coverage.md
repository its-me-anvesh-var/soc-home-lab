# MITRE ATT&CK Coverage Map — SOC Home Lab

> Tracks which ATT&CK techniques have been simulated, detected, and documented in this lab.
> Last updated: 2025

---

## Coverage Legend

| Status | Meaning |
|---|---|
| ✅ Simulated + Detected | Attack executed, Splunk/Wazuh alerted correctly |
| 🟡 Simulated, Partial Detection | Attack executed, detection needs tuning |
| 🔲 Planned | Scheduled for future simulation |

---

## Tactic Coverage

### 1. Reconnaissance
| Technique ID | Technique Name | Tool Used | Detection | SPL Query |
|---|---|---|---|---|
| T1046 | Network Service Discovery | Nmap `-sS`, `-sV` | ✅ | port-scan-detection.spl |
| T1595.001 | Active Scanning: Scanning IP Blocks | Nmap `-sn` ping sweep | ✅ | port-scan-detection.spl |

---

### 2. Credential Access
| Technique ID | Technique Name | Tool Used | Detection | SPL Query |
|---|---|---|---|---|
| T1110.001 | Brute Force | Hydra / Metasploit smb_login | ✅ | brute-force-detection.spl |
| T1110.003 | Password Spraying | Metasploit smb_login | ✅ | brute-force-detection.spl |
| T1003.001 | LSASS Memory Dump | Metasploit hashdump | 🟡 | — (planned tuning) |

---

### 3. Lateral Movement
| Technique ID | Technique Name | Tool Used | Detection | SPL Query |
|---|---|---|---|---|
| T1021.002 | SMB/Windows Admin Shares (PsExec) | Metasploit psexec | ✅ | lateral-movement.spl |
| T1078 | Valid Accounts (Pass-the-Hash) | Metasploit | 🟡 | lateral-movement.spl |

---

### 4. Privilege Escalation
| Technique ID | Technique Name | Tool Used | Detection | SPL Query |
|---|---|---|---|---|
| T1134.001 | Token Impersonation/Theft | Metasploit getsystem | ✅ | privilege-escalation.spl |
| T1053.005 | Scheduled Task Creation | schtasks.exe | ✅ | privilege-escalation.spl |

---

### 5. Command & Control
| Technique ID | Technique Name | Tool Used | Detection | SPL Query |
|---|---|---|---|---|
| T1071.001 | Web Protocols (HTTP C2 sim) | Netcat / manual | 🟡 | c2-beacon-detection.spl |
| T1095 | Non-Application Layer Protocol | Netcat raw TCP | ✅ | c2-beacon-detection.spl |

---

### 6. Discovery
| Technique ID | Technique Name | Tool Used | Detection | SPL Query |
|---|---|---|---|---|
| T1087.001 | Account Discovery: Local Account | net user / Metasploit | 🟡 | — (planned) |
| T1049 | System Network Connections Discovery | netstat / Nmap | ✅ | port-scan-detection.spl |

---

### 7. Persistence (Planned)
| Technique ID | Technique Name | Status |
|---|---|---|
| T1547.001 | Registry Run Keys / Startup Folder | 🔲 Planned |
| T1053.005 | Scheduled Task | 🔲 Planned |
| T1136.001 | Create Local Account | 🔲 Planned |

---

## Detection Gaps & Lessons Learned

See [`lessons-learned/detection-gaps.md`](../lessons-learned/detection-gaps.md) for full notes.

**Key findings:**
- Initial brute force threshold of 3 failures was too noisy — raised to 5 within 60s window
- PsExec lateral movement detection requires both EventID 7045 (service install) AND 4648 (explicit creds) for high confidence — single event produces false positives
- Token impersonation is difficult to detect purely from Windows Event Logs; Sysmon process creation events significantly improve coverage
- C2 beacon detection needs baseline of normal outbound traffic before threshold tuning is meaningful

---

## ATT&CK Navigator Layer

The full Navigator layer JSON for this lab's coverage will be added to this directory.
You can visualize it at: https://mitre-attack.github.io/attack-navigator/
