# SOC Home Lab — Build Journal
**Goal:** Build a fully functional SOC lab to prepare for
SOC L1/L2 analyst interviews in India.

**Target roles:** SOC Analyst L1/L2 at TCS, Wipro, Infosys,
HCL, Capgemini, Mphasis, Securonix, Paladion

---

## Lab Architecture

```
Windows 11 Host (16GB RAM, 450GB D drive)
│
├── VirtualBox 7.1.8
│   ├── Splunk-Server VM (Ubuntu 22.04 LTS)
│   │   └── Splunk Enterprise 10.2.3 (SIEM)
│   │
│   ├── Windows-Endpoint VM (Windows 10 Pro 22H2)
│   │   ├── Sysmon (endpoint telemetry)
│   │   └── Splunk Universal Forwarder (coming Day 3)
│   │
│   └── Kali the Attacker VM (attack simulation)
│       └── Used for controlled attack simulation
│
└── macOS M2 (Analyst Workstation — browser only)
    └── http://127.0.0.1:8000 → Splunk Web UI
```

## Data Flow
```
Windows-Endpoint VM
    → Sysmon (generates rich telemetry)
        → Universal Forwarder (ships logs)
            → Splunk Server port 9997
                → Indexed and searchable
                    → Analyst browser port 8000
```

---

## Tool Stack

| Category | Tool | Version | Status |
|---|---|---|---|
| Hypervisor | VirtualBox | 7.1.8 | ✅ Running |
| OS (SIEM server) | Ubuntu Server | 22.04.5 LTS | ✅ Running |
| SIEM | Splunk Enterprise | 10.2.3 | ✅ Running |
| Endpoint OS | Windows 10 Pro | 22H2 | ✅ Running |
| Endpoint monitor | Sysmon | Latest | ✅ Running |
| Log shipper | Splunk Universal Forwarder | TBD | 🔜 Day 3 |
| EDR | Wazuh | TBD | 🔜 Week 2 |
| Threat Intel | MISP + VirusTotal | TBD | 🔜 Week 4 |
| Network IDS | Suricata | TBD | 🔜 Week 5 |
| Attack Sim | Atomic Red Team + Kali | TBD | 🔜 Week 3 |

---

## Day 1 — Lab Foundation ✅ COMPLETE
**Date:** 10 May 2026
**Duration:** ~4 hours

### What was built
- [x] Created SOC-Lab folder structure on D:\SOC-Lab\
- [x] Downloaded Ubuntu 22.04.5 LTS Server ISO (1.99GB)
- [x] Installed VirtualBox 7.1.8 + Extension Pack
- [x] Created Splunk-Server VM (4GB RAM, 2 CPU, 50GB disk)
- [x] Installed Ubuntu Server 22.04.5 LTS from scratch
- [x] Enabled OpenSSH during installation
- [x] First successful login to Ubuntu terminal
- [x] Updated all Ubuntu packages (71 packages)
- [x] Downloaded Splunk Enterprise 10.2.3 (1.2GB)
- [x] Installed Splunk Enterprise
- [x] Created dedicated splunk service user
- [x] Set correct file permissions on /opt/splunk
- [x] Started Splunk successfully
- [x] Configured NAT port forwarding (Host 8000 → VM 8000)
- [x] Accessed Splunk Web UI from host browser ✅
- [x] Enabled Splunk auto-start on boot
- [x] Took VM snapshot — Day1-Complete

---

## Day 2 — Windows Endpoint + Sysmon + SPL Learning ✅ COMPLETE
**Date:** 11 May 2026
**Duration:** ~4 hours

### What was built
- [x] Learned 10 core SPL commands in Splunk
- [x] Built first investigation-style searches
- [x] Learned noise filtering with != operator
- [x] Learned timechart for attack timeline analysis
- [x] Learned eval and case() for alert classification
- [x] Learned rex for data extraction from log messages
- [x] Downloaded Windows 10 22H2 ISO (via Mac browser trick)
- [x] Created Windows-Endpoint VM (2GB RAM, 2 CPU, 50GB disk)
- [x] Configured port forwarding for RDP (Host 3389 → VM 3389)
- [x] Installed Windows 10 Pro 22H2
- [x] Configured privacy settings (all off)
- [x] Created analyst user account
- [x] Installed Sysmon with SwiftOnSecurity config
- [x] Verified Sysmon running — sc query sysmon64 → RUNNING
- [x] Verified Sysmon generating telemetry via Get-WinEvent
- [x] Took VM snapshot — Day2-Sysmon-Installed
- [ ] Install Splunk Universal Forwarder (Day 3)
- [ ] Configure UF to forward logs to Splunk (Day 3)
- [ ] Verify Windows logs appear in Splunk (Day 3)

---

## Troubleshooting Log

### Issue 1 — VirtualBox Host-Only Adapter Error
- **Error:** VERR_INTNET_FLT_IF_NOT_FOUND
- **Cause:** Windows 11 driver conflict with VirtualBox 7.1.8
- **Attempted:** VBoxNetAdpCtl, netlwf reinstall, full VirtualBox reinstall
- **Fix:** Disabled Adapter 2 entirely, used NAT only with port forwarding
- **Status:** Resolved ✅
- **Learning:** Host-Only networking has known issues on Windows 11
  with VirtualBox 7.x. NAT + port forwarding is equally valid.

### Issue 2 — wget flag typo (-0 vs -O)
- **Error:** wget: invalid option -- '0'
- **Cause:** Typed -0 (zero) instead of -O (capital O)
- **Fix:** Reran command with correct -O flag
- **Status:** Resolved ✅
- **Learning:** Linux is case sensitive. -O means output filename.

### Issue 3 — Corrupted Splunk .deb download
- **Error:** splunk.deb is not a Debian format archive
- **Cause:** First download incomplete due to flag typo
- **Fix:** Redownloaded correct file, verified 1.2GB size
- **Status:** Resolved ✅
- **Learning:** Always verify file size after download before installing.

### Issue 4 — Splunk refusing to run as root
- **Error:** Running Splunk Enterprise as root is deprecated
- **Cause:** Splunk 10.x blocks root execution by default
- **Fix:** Created dedicated splunk user, changed ownership, ran as splunk user
- **Commands used:**
```bash
sudo useradd -m splunk
sudo chown -R splunk:splunk /opt/splunk
sudo -u splunk /opt/splunk/bin/splunk start \
  --accept-license --answer-yes \
  --no-prompt --seed-passwd SOCadmin@123
```
- **Status:** Resolved ✅
- **Learning:** Principle of Least Privilege — never run services as root.

### Issue 5 — Browser could not reach VM on 10.0.2.15
- **Error:** Browser timeout on http://10.0.2.15:8000
- **Cause:** NAT networking isolates VM from host
- **Fix:** Added VirtualBox port forwarding: Host 127.0.0.1:8000 → Guest 10.0.2.15:8000
- **Status:** Resolved ✅
- **Learning:** NAT = one way. Port forwarding = punch a hole for specific ports.

### Issue 6 — boot-start permission denied
- **Error:** Can't create RC file: Permission denied
- **Cause:** boot-start needs root to write system startup files
- **Fix:** sudo /opt/splunk/bin/splunk enable boot-start -user splunk
- **Status:** Resolved ✅
- **Learning:** Some commands need root to write files but run service as low-priv user.

### Issue 7 — Windows 10 ISO download stuck at 0%
- **Cause:** MediaCreationTool unreliable on slow connections
- **Fix:** Used Chrome DevTools to spoof Mac user-agent for direct ISO link.
  Also downloaded via MacBook Safari which shows direct ISO automatically.
- **Status:** Resolved ✅
- **Learning:** Microsoft shows direct ISO only to non-Windows browsers.

### Issue 8 — Mac to Windows file transfer
- **Cause:** No USB, SMB share needs password (PIN doesnt work for SMB)
- **Fix:** Downloaded ISO directly on Windows using user-agent spoofing
- **Status:** Resolved ✅
- **Learning:** Always have multiple transfer methods ready.

---

## Commands Reference

```bash
# ── LINUX / UBUNTU ──────────────────────────────

whoami                              # current user
ip a                                # show all IPs
ip a | grep "inet "                 # show only IPs
df -h                               # disk space
free -h                             # RAM usage
sudo apt update                     # refresh packages
sudo apt upgrade -y                 # install updates
wget -O filename.deb "URL"          # download file
sudo dpkg -i package.deb            # install .deb
sudo useradd -m username            # create user
sudo chown -R user:user /path       # change ownership
sudo shutdown now                   # safe shutdown

# Splunk commands
sudo -u splunk /opt/splunk/bin/splunk start
sudo -u splunk /opt/splunk/bin/splunk stop
sudo -u splunk /opt/splunk/bin/splunk status
sudo -u splunk /opt/splunk/bin/splunk restart
sudo /opt/splunk/bin/splunk enable boot-start -user splunk

# Network
ss -tlnp | grep 8000                # check port 8000
ping 8.8.8.8                        # test internet
```

```powershell
# ── WINDOWS ─────────────────────────────────────

# Check Sysmon service
sc query sysmon64

# View Sysmon events
Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' -MaxEvents 5 | Format-List TimeCreated, Id, Message

# Install Sysmon
sysmon64.exe -accepteula -i C:\Users\analyst\Desktop\sysmonconfig.xml
```

```splunk
# ── SPLUNK SPL ───────────────────────────────────

# Basic search with limit
index=_internal | head 10

# Count events by field
index=windows | stats count by EventCode | sort -count

# Exclude noise
index=windows EventCode!=4634 EventCode!=4624
| stats count by EventCode | sort -count

# Clean investigation view
index=windows EventCode=4625
| table _time, host, TargetUserName, src_ip

# Auto-classify severity
index=windows
| eval severity=case(
    EventCode==4625, "High",
    EventCode==4624, "Medium",
    EventCode==4634, "Low"
  )
| table _time, EventCode, severity

# Attack timeline
index=windows | timechart count span=5m

# Extract encoded strings
index=windows
| rex field=CommandLine "(?P<encoded>[A-Za-z0-9+/=]{20,})"
| where isnotnull(encoded)

# Brute force detection
index=windows EventCode=4625
| stats count by TargetUserName, src_ip
| sort -count

# Password spray detection
index=windows EventCode=4625
| stats dc(TargetUserName) AS unique_users, count by src_ip
| where unique_users > 10
```

---

## SPL Commands Reference Table

| Command | What it does | SOC Use Case |
|---|---|---|
| `head N` | Show first N results | Quick preview |
| `stats count by field` | Count and group events | Find top attackers |
| `sort -count` | Sort highest to lowest | Prioritize findings |
| `!=` | Exclude values | Remove noise |
| `table field1, field2` | Clean column display | Investigation view |
| `eval newfield=value` | Create calculated field | Add context |
| `case()` | If/else classification | Auto-severity |
| `timechart count span=Xm` | Timeline graph | Attack timeline |
| `rex field=X "pattern"` | Extract from text | IOC extraction |
| `where isnotnull(field)` | Filter empty fields | Verify extraction |
| `dc(field)` | Count distinct values | Spray detection |

---

## Windows Event IDs — Must Memorize

| Event ID | What it means | SOC Priority |
|---|---|---|
| 4624 | Successful login | Medium |
| 4625 | Failed login | High |
| 4634 | Logoff | Low |
| 4648 | Explicit credential login | High |
| 4688 | Process creation | High |
| 4698 | Scheduled task created | High |
| 4720 | User account created | High |
| 4740 | Account locked out | Medium |
| 4776 | NTLM auth attempt | Medium |
| 5140 | Network share accessed | Medium |

## Sysmon Event IDs — Must Memorize

| Event ID | What it captures | Why it matters |
|---|---|---|
| 1 | Process creation + command line | Catch malware execution |
| 3 | Network connection + destination IP | Catch C2 beaconing |
| 7 | DLL loaded | Catch DLL hijacking |
| 11 | File created | Catch dropped payloads |
| 13 | Registry value set | Catch persistence |
| 22 | DNS query | Catch C2 domains |

## Windows Logon Types — Must Memorize

| Type | Name | SOC Significance |
|---|---|---|
| 2 | Interactive | Physical login — normal |
| 3 | Network | SMB/file share — watch for lateral movement |
| 4 | Batch | Scheduled task — check for persistence |
| 5 | Service | Service start — check for malicious services |
| 7 | Unlock | Screen unlock — normal |
| 10 | RemoteInteractive | RDP — external = P1 |
| 11 | CachedInteractive | Offline cached login |

---

## Incident Priority Classification

| Priority | Response Time | Example Scenario |
|---|---|---|
| P1 Critical | 15 minutes | Active ransomware, external RDP at 3am, exfiltration |
| P2 High | 30 minutes | Malware detected, successful brute force, privesc |
| P3 Medium | 2 hours | Suspicious process, multiple failed logins |
| P4 Low | Same day | Single failed login, routine scan |

### 3 questions to decide priority:
1. Is it active or historical? Active = higher
2. How many systems affected? More = higher
3. What data is at risk? PII/financial = higher

---

## Key Concepts

| Concept | SOC Context |
|---|---|
| Hypervisor | Creates isolated VMs — like SOC jump boxes |
| NAT + Port Forwarding | VM internet + specific port exposure |
| Principle of Least Privilege | Never run services as root |
| splunkd | Splunk background daemon — port 8000 web, port 9997 forwarder |
| Sysmon | Free Microsoft tool — 29 endpoint event types |
| SwiftOnSecurity config | Industry standard Sysmon configuration |
| TargetUserName | Account being attacked in Windows logs |
| SubjectUserName | Account performing the action |
| Noise reduction | Excluding irrelevant events to focus on threats |
| timechart | Visualize attack start time and spread |
| P1/P2/P3/P4 | Incident severity classification system |

---

## Screenshots

| File | What it shows | Status |
|---|---|---|
| 01-ubuntu-first-login.png | First Ubuntu login | ✅ |
| 02-ubuntu-updated.png | apt upgrade complete | ✅ |
| 03-splunk-started.png | Splunk daemon started | ✅ |
| 04-splunk-web-ui.png | Splunk Web UI in browser | ✅ |
| 05-windows-endpoint-desktop.png | Windows 10 VM desktop | ✅ |
| 06-sysmon-installed.png | Sysmon service running | ✅ |
| 07-uf-configured.png | Universal Forwarder setup | 🔜 Day 3 |
| 08-windows-logs-in-splunk.png | First Windows logs in Splunk | 🔜 Day 3 |

---

## Day 3 Plan
- [ ] Create free Splunk account
- [ ] Download Splunk Universal Forwarder for Windows
- [ ] Install UF on Windows-Endpoint VM
- [ ] Configure UF to point to Splunk Server (10.0.2.15:9997)
- [ ] Configure inputs.conf to monitor Windows Event Log
- [ ] Configure inputs.conf to monitor Sysmon logs
- [ ] Verify logs flowing into Splunk
- [ ] Run first real SOC search on Windows events
- [ ] Build first detection alert in Splunk

---

## Credentials (Lab only — never commit real passwords)
- Ubuntu login: analyst / SOClab@123
- Splunk Web UI: admin / SOCadmin@123
- Splunk URL: http://127.0.0.1:8000
- Splunk forwarder port: 9997
- Windows VM login: analyst / SOClab@123
- Windows VM RDP port: 3389
