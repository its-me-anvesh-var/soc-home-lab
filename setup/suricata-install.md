# 🔧 Suricata IDS Installation Guide

**OS:** Ubuntu 22.04 LTS  
**Author:** Anvesh Raju Vishwaraju

---

## Step 1 — Install Suricata

```bash
sudo add-apt-repository ppa:oisf/suricata-stable -y
sudo apt update
sudo apt install suricata -y

# Verify install
suricata --version
```

---

## Step 2 — Configure Network Interface

```bash
# Find your interface name
ip a
# Example: enp0s3, eth0, ens33

# Edit config
sudo nano /etc/suricata/suricata.yaml
```

Find and update:
```yaml
af-packet:
  - interface: enp0s3   # ← your interface name
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
```

---

## Step 3 — Update Rules

```bash
# Update Emerging Threats rules
sudo suricata-update

# List available rule sources
sudo suricata-update list-sources

# Enable ETPRO (free)
sudo suricata-update enable-source et/open
sudo suricata-update
```

---

## Step 4 — Start & Test

```bash
sudo systemctl enable suricata
sudo systemctl start suricata

# Test with EICAR (safe test file)
curl -o /tmp/eicar.com http://www.eicar.org/download/eicar.com

# Check alerts
sudo tail -f /var/log/suricata/fast.log
sudo tail -f /var/log/suricata/eve.json | python3 -m json.tool
```

---

## Step 5 — Forward to Splunk

```bash
# Add Suricata eve.json to Splunk forwarder
sudo /opt/splunkforwarder/bin/splunk add monitor \
  /var/log/suricata/eve.json \
  -sourcetype suricata \
  -index main
```
