# CodeAlpha_Suricata_IDS
# 🛡️ Suricata IDS: Full Installation & Testing Guide

## 📌 Project Overview

This project provides a full guide for installing and configuring **Suricata IDS** on a Linux VM and testing it using a second VM on the same virtual network. It includes setting up local and custom rules (120+ from GitHub), monitoring alerts, and verifying IDS functionality with simulated attacks.

---

## 🧰 Requirements

- **VM 1 (Suricata Server):**
  - OS: Ubuntu 20.04 or later
  - RAM: 4 GB minimum
  - CPU: 2 cores
- **VM 2 (Attacker/Client):**
  - OS: Kali Linux / Ubuntu / Windows
  - On same virtual network as Suricata VM

---

## ⚙️ Step-by-Step Installation (on Suricata VM)

### 🟢 1. Update System
```bash
sudo apt update && sudo apt upgrade -y
2. Add Suricata Repository

sudo add-apt-repository ppa:oisf/suricata-stable
sudo apt update

🟢 3. Install Suricata

sudo apt install suricata -y

🟢 4. Verify Installation

suricata --build-info


---

🔧 Suricata Configuration

🟡 1. Identify Network Interface

ip a

Example output might show eth0, ens33, or enp0s3.

🟡 2. Set Interface in Config File

Edit /etc/suricata/suricata.yaml:

af-packet:
  - interface: eth0

Change eth0 to your correct network interface.


---

📜 Add Local Rules

🔵 1. Create/Edit Local Rules

sudo nano /etc/suricata/rules/local.rules

🔵 2. Example Rule

alert http any any -> any any (msg:"HTTP Test Detected"; content:"/test"; sid:1000001; rev:1;)

🔵 3. Ensure Rule is Loaded in Config

Edit suricata.yaml:

rule-files:
  - local.rules


---

🌐 Add 120+ Custom Rules from GitHub

🔴 1. Navigate to Rules Directory

cd /etc/suricata/rules/

🔴 2. Download Rules File

sudo wget https://raw.githubusercontent.com/YOUR_USERNAME/YOUR_REPO/main/rules/custom.rules

> Replace the URL above with the actual GitHub raw file URL.



🔴 3. Include in Config File

Edit suricata.yaml:

rule-files:
  - local.rules
  - custom.rules

🔴 4. Restart Suricata

sudo systemctl restart suricata


---

▶️ Running Suricata

🟢 Start in Background (Daemon Mode)

sudo systemctl start suricata

🟢 Or Run in Foreground (Debug Mode)

sudo suricata -c /etc/suricata/suricata.yaml -i eth0 -v

> Replace eth0 with your actual interface.




testing from another vm
1. Generate HTTP Alert

From attacker VM:

curl http://<SURICATA_VM_IP>/test

🧷 2. Port Scan Detection

nmap -sS <SURICATA_VM_IP>


---

🧾 View Logs and Alerts

📂 1. Fast Alerts

sudo tail -f /var/log/suricata/fast.log

📂 2. Detailed Logs (JSON)

sudo tail -f /var/log/suricata/eve.json

📂 3. Use jq for Formatting

sudo apt install jq -y
jq . /var/log/suricata/eve.json