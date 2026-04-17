# 🌐 Lab Network Configuration Guide

**Author:** Anvesh Raju Vishwaraju

---

## Network Topology

```
Host Machine (Windows/Mac/Linux)
│
├── Host-Only Network: 192.168.56.0/24
│   ├── 192.168.56.10  — Wazuh Manager + Splunk (Ubuntu 22.04)
│   ├── 192.168.56.20  — Windows 11 Victim
│   ├── 192.168.56.30  — Ubuntu Web Server (victim)
│   └── 192.168.56.40  — Kali Linux (attacker)
│
└── NAT Network (for internet on Wazuh only)
    └── 192.168.56.10 gets internet via NAT adapter
```

---

## Step 1 — VirtualBox Host-Only Network

```
1. Open VirtualBox
2. File → Host Network Manager → Create
3. Configure:
   IPv4 Address: 192.168.56.1
   IPv4 Mask:    255.255.255.0
   DHCP Server:  Disabled (use static IPs)
4. Click Apply
```

---

## Step 2 — Assign Static IPs

### Wazuh Manager (Ubuntu 22.04)
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
```yaml
network:
  version: 2
  ethernets:
    enp0s3:           # NAT adapter (internet)
      dhcp4: true
    enp0s8:           # Host-Only adapter
      dhcp4: no
      addresses: [192.168.56.10/24]
```
```bash
sudo netplan apply
```

### Kali Linux (Attacker)
```bash
sudo nano /etc/network/interfaces
# Add:
auto eth1
iface eth1 inet static
  address 192.168.56.40
  netmask 255.255.255.0
```

### Windows 11 (Victim)
```
Control Panel → Network → Adapter Settings
→ Host-Only Adapter → Properties → IPv4
IP: 192.168.56.20
Mask: 255.255.255.0
Gateway: 192.168.56.1
```

---

## Step 3 — Verify Connectivity

```bash
# From Kali — ping all hosts
ping 192.168.56.10   # Wazuh Manager
ping 192.168.56.20   # Windows victim
ping 192.168.56.30   # Ubuntu web server

# From Wazuh — verify agent connections
sudo /var/ossec/bin/agent_control -l
```

---

## Firewall Rules (Wazuh Manager)

```bash
# Allow Wazuh agent traffic
sudo ufw allow 1514/udp
sudo ufw allow 1515/tcp
sudo ufw allow 55000/tcp

# Allow Splunk
sudo ufw allow 8000/tcp
sudo ufw allow 9997/tcp

# Allow dashboard
sudo ufw allow 443/tcp

sudo ufw enable
```
