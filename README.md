# 🛡️ SOC Home Lab

A fully functional Security Operations Centre (SOC) home lab built using open-source tools — Wazuh, Splunk, and Suricata — with MITRE ATT&CK mapped detection rules, incident response playbooks, and automated alert triage workflows.

---

## 🏗️ Lab Architecture

```
┌─────────────────────────────────────────────────────┐
│                   SOC HOME LAB                      │
│                                                     │
│  ┌─────────────┐      ┌──────────────────────────┐  │
│  │  Kali Linux │      │     Wazuh Manager        │  │
│  │  Attacker   │─────▶│  (SIEM + HIDS)           │  │
│  └─────────────┘      │  Ubuntu 22.04            │  │
│                       └──────────┬───────────────┘  │
│  ┌─────────────┐                 │                  │
│  │ Windows 11  │◀────────────────┤                  │
│  │ Victim Host │   Wazuh Agent   │                  │
│  └─────────────┘                 │                  │
│                       ┌──────────▼───────────────┐  │
│  ┌─────────────┐      │     Splunk Enterprise    │  │
│  │ Ubuntu 22   │─────▶│  (Log Analysis + Alerts) │  │
│  │ Web Server  │      └──────────────────────────┘  │
│  └─────────────┘                                    │
└─────────────────────────────────────────────────────┘
```

---

## 🛠️ Tools Used

| Tool | Role | Version |
|---|---|---|
| Wazuh | SIEM / HIDS / FIM | 4.7 |
| Splunk | Log analysis / Dashboards | Enterprise Trial |
| Suricata | Network IDS | 7.x |
| VirtualBox | Hypervisor | 7.x |
| Kali Linux | Attack simulation | 2024.x |
| Windows 11 | Victim endpoint | - |
| Ubuntu 22.04 | Wazuh manager + web server | - |

---

## 📂 Repository Structure

```
soc-home-lab/
├── README.md
├── setup/
│   ├── wazuh-install.md          # Wazuh manager setup guide
│   ├── splunk-install.md         # Splunk install + config
│   ├── suricata-install.md       # Suricata IDS setup
│   └── network-config.md         # Lab network configuration
├── detection-rules/
│   ├── splunk/
│   │   ├── brute-force.spl       # Brute force detection
│   │   ├── data-exfiltration.spl # Large outbound transfer alert
│   │   ├── after-hours-login.spl # Off-hours admin login
│   │   └── powershell-abuse.spl  # Malicious PowerShell detection
│   └── wazuh/
│       ├── custom-rules.xml      # Custom Wazuh detection rules
│       └── decoder.xml           # Custom log decoders
├── playbooks/
│   ├── phishing-response.md      # Phishing incident playbook
│   ├── ransomware-response.md    # Ransomware incident playbook
│   └── brute-force-response.md   # Brute force response steps
├── mitre-mappings/
│   └── lab-coverage.md           # MITRE ATT&CK coverage map
└── screenshots/
    └── (add your dashboard screenshots here)
```

---

## ⚙️ Quick Setup Guide

### Prerequisites
- VirtualBox 7.x installed
- Minimum 16GB RAM
- 200GB free disk space
- Internet connection for package downloads

### Step 1 — Network Setup
Create a Host-Only network in VirtualBox:
```
File → Host Network Manager → Create
IPv4: 192.168.56.1
Mask: 255.255.255.0
DHCP: Enabled
```

### Step 2 — Deploy Wazuh Manager (Ubuntu 22.04)
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Wazuh (single-node deployment)
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
sudo bash wazuh-install.sh -a

# Access dashboard
# https://localhost (default credentials in install output)
```

### Step 3 — Deploy Wazuh Agent (Windows 11)
```powershell
# Download agent MSI from Wazuh dashboard
# Manager IP: 192.168.56.10
# Run installer and register with manager
```

### Step 4 — Deploy Splunk
```bash
# Download Splunk Enterprise trial from splunk.com
tar -xvzf splunk-*.tgz -C /opt
sudo /opt/splunk/bin/splunk start --accept-license
# Access: http://localhost:8000
```

---

## 🎯 Detection Rules

### MITRE ATT&CK Coverage

| Tactic | Technique | Detection Rule | Status |
|---|---|---|---|
| Initial Access | T1566 Phishing | Email gateway + Wazuh | ✅ |
| Execution | T1059 PowerShell | powershell-abuse.spl | ✅ |
| Persistence | T1136 Create Account | custom-rules.xml | ✅ |
| Credential Access | T1110 Brute Force | brute-force.spl | ✅ |
| Lateral Movement | T1021 Remote Services | custom-rules.xml | ✅ |
| Exfiltration | T1041 Exfil over C2 | data-exfiltration.spl | ✅ |
| Discovery | T1082 System Info | Wazuh FIM | ✅ |
| Defense Evasion | T1562 Disable Tools | custom-rules.xml | ✅ |

---

## 📖 Incident Response Playbooks

- [Phishing Response](playbooks/phishing-response.md)
- [Ransomware Response](playbooks/ransomware-response.md)
- [Brute Force Response](playbooks/brute-force-response.md)

---

## 📊 Attack Simulations Run

| Simulation | Tool Used | Alert Triggered |
|---|---|---|
| SSH Brute Force | Hydra | ✅ Yes |
| PowerShell Reverse Shell | Metasploit | ✅ Yes |
| Data Exfiltration | Custom script | ✅ Yes |
| New Admin Account | Manual | ✅ Yes |
| Lateral Movement via RDP | Metasploit | ✅ Yes |

---

## 🏅 Certifications & Background

Built by **Anvesh Raju Vishwaraju**
- CompTIA Security+ | eJPTv2 | AWS Cloud Practitioner
- M.S. Cybersecurity — UNC Charlotte, USA
- Lecturer, Cybersecurity & AI — Malla Reddy College

🔗 [LinkedIn](https://linkedin.com/in/arv007) | [GitHub](https://github.com/its-me-anvesh-var)

---

## ⚠️ Disclaimer

This lab is for educational purposes only. All attack simulations are conducted in an isolated environment. Never use these techniques on systems you do not own or have explicit permission to test.
