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
- [x] Downloaded Splunk Enterprise 10.2.3 .deb package (1.2GB)
- [x] Installed Splunk Enterprise 10.2.3
- [x] Created dedicated splunk system user
- [x] Set correct file permissions for Splunk
- [x] Started Splunk successfully — web server running on port 8000
- [ ] Accessed Splunk Web UI from host browser (next step)

### Phase 3 — Splunk Running ✅
- [x] Splunk daemon (splunkd) started successfully
- [x] SSL certificate generated (self-signed)
- [x] Web interface available at http://127.0.0.1:8000
- [ ] Configured Splunk auto-start on boot
- [ ] Accessed from host Windows browser

## Troubleshooting Log

### Issue 3 — Splunk refusing to run as root
- **Error:** "Running Splunk Enterprise as root is deprecated"
- **Cause:** Splunk 10.x blocks root execution by default
- **Fix:** Created dedicated splunk user, changed ownership 
  of /opt/splunk to splunk user, ran Splunk as splunk user
- **Commands used:**
```bash
  sudo useradd -m splunk
  sudo chown -R splunk:splunk /opt/splunk
  sudo -u splunk /opt/splunk/bin/splunk start \
    --accept-license --answer-yes \
    --no-prompt --seed-passwd SOCadmin@123
```
- **Status:** Resolved ✅
- **Learning:** In production SOCs, Splunk never runs as root. 
  Dedicated service accounts are a security best practice — 
  principle of least privilege.

### Issue 4 — First splunk.deb download corrupted
- **Error:** "splunk.deb is not a Debian format archive"
- **Cause:** First wget used wrong flag (-0 zero vs -O capital O),
  file was incomplete/corrupted
- **Fix:** Redownloaded with correct filename, installed 
  splunk-10.2.3-4d61cf8a5c0c-linux-amd64.deb
- **Status:** Resolved ✅
- **Learning:** Always verify downloaded file size matches 
  expected size before installing packages.

## Key Concepts Learned Today
- **Hypervisor:** Software that creates and runs VMs
- **NAT Network:** VM gets internet via host machine
- **SSH:** How SOC analysts remotely access servers
- **.deb package:** Ubuntu's installer format
- **apt:** Ubuntu's package manager
- **sudo:** Run command as administrator in Linux
- **Principle of Least Privilege:** Never run services 
  as root — use dedicated service accounts
- **splunkd:** The Splunk daemon (background service) 
  that runs the SIEM engine
- **Port 8000:** Default Splunk web UI port

## Commands Reference
```bash
# Check system info
ip a                    # show IP addresses
df -h                   # check disk space
free -h                 # check RAM

# Update Ubuntu
sudo apt update && sudo apt upgrade -y

# Download Splunk
wget -O splunk.deb "https://download.splunk.com/..."

# Install Splunk
sudo dpkg -i splunk-10.2.3-4d61cf8a5c0c-linux-amd64.deb

# Create splunk user
sudo useradd -m splunk
sudo chown -R splunk:splunk /opt/splunk

# Start Splunk
sudo -u splunk /opt/splunk/bin/splunk start \
  --accept-license --answer-yes \
  --no-prompt --seed-passwd SOCadmin@123

# Check Splunk status
sudo -u splunk /opt/splunk/bin/splunk status

# Check which port Splunk is on
ss -tlnp | grep 8000
```

## Screenshots to Capture
- [x] 01-ubuntu-first-login.png
- [x] 02-ubuntu-updated.png
- [x] 03-splunk-started.png
- [ ] 04-splunk-web-ui.png ← after browser access
