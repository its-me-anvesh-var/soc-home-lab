# SOC Home Lab — Build Journal

## Lab Architecture
- Host Machine: Windows 11 (16GB RAM, 450GB D drive)
- Analyst Workstation: macOS (browser-based access)
- Hypervisor: VirtualBox 7.1.8

## Node 1: Splunk-Server (Ubuntu VM)
- OS: Ubuntu 22.04.5 LTS (Jammy Jellyfish)
- RAM: 4096 MB
- Storage: 50 GB
- Role: SIEM server — runs Splunk Enterprise
- Network: NAT (internet access)

## Data Flow
Endpoint VM → Universal Forwarder → Splunk Server → Analyst Browser

## Day 1 Progress

### Phase 1 — Lab Foundation ✅
- [x] Created SOC-Lab folder structure on D:\SOC-Lab\
- [x] Downloaded Ubuntu 22.04.5 LTS Server ISO (1.99GB)
- [x] Installed VirtualBox 7.1.8 + Extension Pack
- [x] Created Splunk-Server VM (4GB RAM, 2 CPU, 50GB disk)
- [x] Configured VM network (NAT adapter)
- [x] Installed Ubuntu Server 22.04.5 LTS (Jammy Jellyfish)
- [x] Enabled OpenSSH during installation
- [x] First successful login to Ubuntu VM

### Phase 2 — Ubuntu Configuration ✅
- [x] Updated all Ubuntu packages (apt update && apt upgrade)
- [x] 71 packages updated successfully
- [ ] Downloaded Splunk Enterprise 9.4.1 .deb package (in progress)
- [ ] Installed Splunk Enterprise
- [ ] Configured Splunk auto-start on boot
- [ ] Accessed Splunk Web UI from host browser

## Troubleshooting Log

### Issue 1 — VirtualBox Host-Only Adapter Error
- **Error:** VERR_INTNET_FLT_IF_NOT_FOUND
- **Cause:** Windows 11 driver conflict with VirtualBox 7.1.8
- **Attempted fixes:** VBoxNetAdpCtl, netlwf driver reinstall, 
  full VirtualBox reinstall via Modify
- **Final fix:** Disabled Adapter 2 entirely, using NAT only
- **Status:** Resolved ✅

### Issue 2 — wget capital O vs zero typo
- **Error:** wget: invalid option -- '0'
- **Cause:** Typed -0 (zero) instead of -O (capital O)
- **Fix:** Reran command with correct -O flag
- **Status:** Resolved ✅
- **Learning:** Linux commands are case sensitive. 
  Always double check flags before pressing Enter.

## Key Concepts Learned Today
- **Hypervisor:** Software that creates and runs VMs 
  (VirtualBox is Type 2 — runs on top of Windows)
- **NAT Network:** VM gets internet via host machine's 
  connection — like your phone sharing WiFi
- **SSH:** Secure Shell — how SOC analysts remotely 
  access servers without sitting in front of them
- **.deb package:** Ubuntu's installer format 
  (equivalent of .exe on Windows)
- **apt:** Ubuntu's package manager — like Microsoft 
  Store but for Linux tools
- **sudo:** Run a command as administrator in Linux
  (equivalent of "Run as Administrator" on Windows)

## Commands Used Today
```bash
# Check system info after login
ip a                    # show IP addresses
df -h                   # check disk space
free -h                 # check RAM usage

# Update Ubuntu
sudo apt update         # refresh package list
sudo apt upgrade -y     # install all updates

# Download Splunk
wget -O splunk.deb "https://download.splunk.com/..."
```

## Screenshots Captured
- 01-ubuntu-first-login.png
- 02-ubuntu-updated.png
