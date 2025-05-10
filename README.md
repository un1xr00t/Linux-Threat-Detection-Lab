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
- Firewall: Allow SSH and webhook ports (if using Slack/Discord)

### 2. Install & Configure Auditd
```bash
sudo apt update && sudo apt install auditd -y
sudo systemctl enable --now auditd
****
