# 🛡️ Linux Threat Detection Lab – Auditd + Falco + Custom Alerts

A cloud-based detection lab designed to simulate and monitor real-world threats on a Linux server using open-source tools like `auditd`, `Falco`, and custom alert scripts. Deployed and tested on a Linode instance (Ubuntu 22.04).

---

## 🚀 Project Goals

- Monitor and detect suspicious activity on a Linux host
- Simulate realistic attack techniques (privilege escalation, file tampering, unauthorized tool usage)
- Send real-time alerts to Slack/Discord via custom webhook integrations
- Gain practical experience with audit rules, runtime security, and alert pipelines

---

## 🧰 Tools & Technologies

| Tool        | Purpose                                       |
|-------------|-----------------------------------------------|
| `auditd`    | Kernel-level auditing of system events        |
| `Falco`     | Runtime detection engine for Linux            |
| `bash/python` | Custom alert triggers and webhook integration |
| `Linode`    | Cloud hosting for public access and realism   |

---

## 🧪 Simulated Attack Scenarios

| Scenario                                              | Detection Logic (Auditd/Falco)                   |
|-------------------------------------------------------|--------------------------------------------------|
| Editing `/etc/passwd` or `/etc/shadow`               | Watch rules + Falco file access detection        |
| Unauthorized use of `sudo` or `su`                   | Auditd syscall monitoring, Falco `execve` checks |
| Execution of hacking tools (`nmap`, `netcat`)        | Falco `proc.name` or command-line matching       |
| Adding user to `sudoers`                             | File watch + group modification audit rule       |
| SSH login from unknown IP address                    | `/var/log/auth.log` + custom script detection    |

---

## ⚙️ Setup Guide

### 1. Launch Linode Instance
- OS: Ubuntu 22.04
- Firewall: Allow SSH and webhook ports (e.g., 22, 443)
upgrade your instance with:
```bash
sudo apt update && sudo apt upgrade
```
setup UFW and allow the ports 22 and 443
```bash
sudo apt install ufw -y
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Allow SSH and Webhook ports
sudo ufw allow 22/tcp    # SSH
sudo ufw allow 443/tcp   # Webhooks (Slack/Discord/etc.)

# Enable UFW
sudo ufw enable

# Confirm UFW status
sudo ufw status verbose
```

---

### 2. Install & Configure Auditd
```bash
sudo apt update && sudo apt install -y auditd
sudo systemctl enable --now auditd
```

### 3. Install & Configure Falco

Falco is a powerful open-source runtime security tool that detects suspicious behavior on Linux systems.

#### Step 1: Install Required Dependencies

```bash
sudo apt update && sudo apt install -y curl gnupg apt-transport-https
```

#### Step 2: Add Falco GPG Key and Repository

```bash
curl -fsSL https://falco.org/repo/falcosecurity-packages.asc | sudo gpg --dearmor -o /usr/share/keyrings/falco-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/falco-archive-keyring.gpg] https://download.falco.org/packages/deb stable main" | sudo tee /etc/apt/sources.list.d/falcosecurity.list
```

#### Step 3: Update Package List

```bash
sudo apt update
```

#### Step 4: Install Falco (modern eBPF version)

```bash
sudo apt install -y falco
```

#### Step 5: Start & Monitor Falco

You are likely using the modern eBPF-based version of Falco which uses a different systemd unit name:

```bash
# Start (if not already running)
sudo systemctl start falco-modern-bpf

# Enable at boot (optional)
sudo systemctl enable falco-modern-bpf

# View live Falco alerts
sudo journalctl -fu falco-modern-bpf
```
---

### 4. (Optional) Add Slack Alert Script

Use a webhook to send Falco or custom alerts to Slack.

```bash
sudo nano /usr/local/bin/send_slack_alert.sh
```

Paste this script (replace the webhook URL with yours):

```bash
#!/bin/bash
WEBHOOK_URL="https://hooks.slack.com/services/XXX/YYY/ZZZ"
MESSAGE="[\$(hostname)] ALERT: \$1"
curl -s -X POST -H 'Content-type: application/json' \
  --data "{\"text\":\"\$MESSAGE\"}" \$WEBHOOK_URL
```

Make the script executable:

```bash
sudo chmod +x /usr/local/bin/send_slack_alert.sh
```

Send a test alert:

```bash
/usr/local/bin/send_slack_alert.sh "Test alert from Linux Threat Detection Lab"
```

---

### 5. Simulate Attacks to Trigger Alerts

Try these commands to test your setup:

```bash
sudo su
nano /etc/passwd
nmap -sS 127.0.0.1
```

Check for alerts in Falco logs or your Slack channel.
