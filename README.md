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
- [x] Created SOC-Lab folder structure on D drive
- [x] Downloaded Ubuntu 22.04.5 LTS Server ISO
- [x] Installed VirtualBox 7.1.8 + Extension Pack
- [x] Created Splunk-Server VM (4GB RAM, 2 CPU, 50GB disk)
- [x] Configured VM network (NAT adapter)
- [x] Installed Ubuntu Server 22.04.5 LTS
- [x] Enabled OpenSSH during installation

## Troubleshooting Log
**Issue:** VirtualBox Host-Only Adapter error (VERR_INTNET_FLT_IF_NOT_FOUND)
**Cause:** Windows 11 driver conflict with VirtualBox 7.1.8 Host-Only networking
**Fix:** Disabled Adapter 2 (Host-Only) — using NAT only for now. 
Will configure internal networking from inside Ubuntu after install.

## Credentials (NEVER push real passwords to GitHub — this is lab only)
- Ubuntu user: analyst
- Ubuntu hostname: splunk-server

## Next Steps (Day 2)
- [ ] First login to Ubuntu VM
- [ ] Update Ubuntu packages
- [ ] Download and install Splunk Enterprise
- [ ] Configure Splunk to start on boot
- [ ] Access Splunk web UI from host browser
