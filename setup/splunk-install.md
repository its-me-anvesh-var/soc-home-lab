# 🔧 Splunk Enterprise Installation Guide

**OS:** Ubuntu 22.04 LTS  
**Splunk Version:** Enterprise Trial (60 days, 500MB/day)  
**Author:** Anvesh Raju Vishwaraju

---

## Step 1 — Download Splunk

```bash
# Download from splunk.com (requires free account)
# Or use wget with direct link:
wget -O splunk-latest-linux-amd64.tgz \
  "https://download.splunk.com/products/splunk/releases/9.2.0/linux/splunk-9.2.0-linux-amd64.tgz"

# Extract to /opt
sudo tar -xvzf splunk-latest-linux-amd64.tgz -C /opt
```

---

## Step 2 — Initial Setup

```bash
# Start Splunk and accept license
sudo /opt/splunk/bin/splunk start --accept-license

# Set admin password when prompted
# Username: admin
# Password: (choose something strong)

# Enable auto-start on boot
sudo /opt/splunk/bin/splunk enable boot-start
```

---

## Step 3 — Access Web UI

```
URL:      http://192.168.56.10:8000
Username: admin
Password: (set during install)
```

---

## Step 4 — Configure Log Inputs

### 4a — Enable Syslog Input (UDP 514)
```bash
# In Splunk Web: Settings → Data Inputs → UDP → New
# Port: 514
# Source type: syslog
# Index: main
```

### 4b — Install Splunk Universal Forwarder on Windows

```powershell
# Download UF from splunk.com
# Install with deployment server pointing to Splunk manager

msiexec.exe /i splunkforwarder-9.2.0-windows-x64.msi /q `
  SPLUNK_ENABLE_SERVICE=1 `
  DEPLOYMENT_SERVER="192.168.56.10:8089"

# Configure inputs on Windows forwarder
# C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf

[WinEventLog://Security]
disabled = 0
index = main

[WinEventLog://System]
disabled = 0
index = main

[WinEventLog://Application]
disabled = 0
index = main
```

### 4c — Configure Linux Forwarder

```bash
# Install UF on Linux target
wget -O splunkforwarder.tgz "https://download.splunk.com/products/universalforwarder/releases/9.2.0/linux/splunkforwarder-9.2.0-linux-amd64.tgz"
sudo tar -xvzf splunkforwarder.tgz -C /opt

sudo /opt/splunkforwarder/bin/splunk start --accept-license
sudo /opt/splunkforwarder/bin/splunk add forward-server 192.168.56.10:9997

# Monitor syslog
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log/syslog -index main
sudo /opt/splunkforwarder/bin/splunk add monitor /var/log/auth.log -index main
```

---

## Step 5 — Create Detection Alerts

```bash
# In Splunk Web: Search & Reporting → Search
# Paste SPL from detection-rules/splunk/*.spl
# Click Save As → Alert
# Set: Trigger condition, throttle, action (email/webhook)
```

---

## Step 6 — Import Detection Rules

All SPL rules are in `detection-rules/splunk/`:

| File | Alert Name | Severity |
|---|---|---|
| brute-force.spl | SSH/RDP Brute Force | HIGH |
| data-exfiltration.spl | Large Data Transfer | CRITICAL |
| after-hours-login.spl | After-Hours Admin Login | MEDIUM |
| powershell-abuse.spl | Malicious PowerShell | HIGH |

---

## Verification Checklist

- [ ] Splunk Web accessible at http://192.168.56.10:8000
- [ ] Windows event logs appearing in Search
- [ ] Linux syslog appearing in Search
- [ ] At least one alert configured and firing
- [ ] Detection rules imported as saved searches
