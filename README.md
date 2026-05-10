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
│   └── Windows 10 VM (coming Day 2)
│       └── Sysmon + Universal Forwarder
│
└── macOS M2 (Analyst Workstation — browser only)
    └── http://127.0.0.1:8000 → Splunk Web UI
```

## Data Flow
```
Windows VM (endpoint)
    → Universal Forwarder
        → Splunk Server (port 9997)
            → Indexed and searchable
                → Analyst browser (port 8000)
```

---

## Tool Stack

| Category | Tool | Version | Status |
|---|---|---|---|
| Hypervisor | VirtualBox | 7.1.8 | ✅ Running |
| OS (SIEM server) | Ubuntu Server | 22.04.5 LTS | ✅ Running |
| SIEM | Splunk Enterprise | 10.2.3 | ✅ Running |
| EDR | Wazuh | TBD | 🔜 Week 2 |
| Endpoint OS | Windows 10 | TBD | 🔜 Day 2 |
| Log source | Sysmon | TBD | 🔜 Day 2 |
| Threat Intel | MISP + VirusTotal | TBD | 🔜 Week 4 |
| Network IDS | Suricata | TBD | 🔜 Week 5 |
| Attack Sim | Atomic Red Team | TBD | 🔜 Week 3 |

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
- [x] Took VM snapshot — Splunk-Installed-Working

---

## Troubleshooting Log

### Issue 1 — VirtualBox Host-Only Adapter Error
- **Error:** VERR_INTNET_FLT_IF_NOT_FOUND
- **Cause:** Windows 11 driver conflict with VirtualBox 7.1.8
- **Attempted:** VBoxNetAdpCtl, netlwf reinstall,
  full VirtualBox reinstall
- **Fix:** Disabled Adapter 2 entirely, used NAT only
  with port forwarding instead
- **Status:** Resolved ✅
- **Learning:** Host-Only networking has known issues on
  Windows 11 with VirtualBox 7.x. NAT + port forwarding
  is equally valid for single-machine labs.

### Issue 2 — wget flag typo (-0 vs -O)
- **Error:** wget: invalid option -- '0'
- **Cause:** Typed -0 (zero) instead of -O (capital O)
- **Fix:** Reran command with correct -O flag
- **Status:** Resolved ✅
- **Learning:** Linux is case sensitive. Always double
  check flags. -O means output filename, -0 means nothing.

### Issue 3 — Corrupted Splunk .deb download
- **Error:** splunk.deb is not a Debian format archive
- **Cause:** First download was incomplete due to flag typo
- **Fix:** Redownloaded correct file, verified 1.2GB size
- **Status:** Resolved ✅
- **Learning:** Verify file size after download before
  installing. Use md5sum to verify checksums on
  production systems.

### Issue 4 — Splunk refusing to run as root
- **Error:** Running Splunk Enterprise as root is deprecated
- **Cause:** Splunk 10.x blocks root execution by default
- **Fix:** Created dedicated splunk user account,
  changed ownership of /opt/splunk, ran as splunk user
- **Commands used:**
```bash
sudo useradd -m splunk
sudo chown -R splunk:splunk /opt/splunk
sudo -u splunk /opt/splunk/bin/splunk start \
  --accept-license --answer-yes \
  --no-prompt --seed-passwd SOCadmin@123
```
- **Status:** Resolved ✅
- **Learning:** Principle of Least Privilege — services
  should never run as root. In real SOCs, Splunk runs
  as a dedicated low-privilege service account.

### Issue 5 — Browser could not reach VM on 10.0.2.15
- **Error:** Browser timeout on http://10.0.2.15:8000
- **Cause:** NAT networking isolates VM from host.
  VM can reach internet but host cannot reach VM directly
- **Fix:** Added VirtualBox port forwarding rule:
  Host 127.0.0.1:8000 → Guest 10.0.2.15:8000
- **Status:** Resolved ✅
- **Learning:** NAT = one way (VM to internet).
  Port forwarding = punch a hole for specific ports.
  This is how cloud firewalls and security groups work too.

### Issue 6 — boot-start permission denied
- **Error:** Can't create RC file: Permission denied
- **Cause:** boot-start needs root to write system startup
  files but was run as splunk user
- **Fix:** sudo /opt/splunk/bin/splunk enable boot-start -user splunk
- **Status:** Resolved ✅
- **Learning:** Some commands need root to write system
  files but run the service as a low-privilege user.
  Common pattern in Linux service management.

---

## Commands Reference

```bash
# System info
whoami                  # current user
ip a                    # show all IP addresses
ip a | grep "inet "     # show only IPs
df -h                   # disk space
free -h                 # RAM usage

# Package management
sudo apt update         # refresh package list
sudo apt upgrade -y     # install all updates

# Download files
wget -O filename.deb "URL"  # capital O = output filename

# Install .deb package
sudo dpkg -i package.deb

# User management
sudo useradd -m username          # create new user
sudo chown -R user:user /path     # change folder ownership

# Splunk service commands
sudo -u splunk /opt/splunk/bin/splunk start
sudo -u splunk /opt/splunk/bin/splunk stop
sudo -u splunk /opt/splunk/bin/splunk status
sudo -u splunk /opt/splunk/bin/splunk restart

# Enable Splunk auto-start on boot
sudo /opt/splunk/bin/splunk enable boot-start -user splunk

# Network checks
ss -tlnp | grep 8000    # check if port 8000 is listening
ping 8.8.8.8            # test internet connectivity
```

---

## Key Concepts Learned — Day 1

| Concept | SOC Context |
|---|---|
| Hypervisor | Creates isolated VMs — same as SOC jump boxes |
| NAT Network | VM shares host internet — like PAT in enterprise |
| Port Forwarding | Exposes specific ports — like firewall rules |
| SSH | Remote server access — how analysts access SIEM |
| .deb package | Ubuntu installer format — like .exe on Windows |
| apt | Package manager — installs and updates Linux tools |
| sudo | Run as admin — like Windows UAC elevation |
| Principle of Least Privilege | Services run as low-priv users |
| splunkd | Splunk background service (daemon) |
| Port 8000 | Default Splunk Web UI port |
| Port 9997 | Default Splunk forwarder receiving port |

---

## Screenshots

| File | What it shows |
|---|---|
| 01-ubuntu-first-login.png | First successful Ubuntu login |
| 02-ubuntu-updated.png | apt upgrade complete |
| 03-splunk-started.png | Splunk daemon started in terminal |
| 04-splunk-web-ui.png | Splunk Web UI in browser |

---

## Day 2 Plan
- [ ] Download Windows 10 ISO
- [ ] Create Windows 10 VM (endpoint)
- [ ] Install Sysmon with SwiftOnSecurity config
- [ ] Install Splunk Universal Forwarder
- [ ] Configure UF to send logs to Splunk
- [ ] Verify Windows Event Logs appear in Splunk
- [ ] Run first SPL search on real data

---

## Credentials (Lab only — never use real passwords here)
- Ubuntu login: analyst / SOClab@123
- Splunk Web UI: admin / SOCadmin@123
- Splunk URL: http://127.0.0.1:8000
- Splunk forwarder port: 9997
