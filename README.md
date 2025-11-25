
# ELK Stack SOC Lab — SSH Brute-Force Attack Detection & Analysis

## Project Overview

This lab demonstrates a Security Operations Center (SOC) workflow using the ELK Stack (Elasticsearch, Logstash, Kibana) to detect, analyze, and respond to SSH brute-force attacks in a virtualized environment.

**Key Skills:**  
Network security monitoring • Log analysis • SIEM integration • Attack simulation • System troubleshooting • Incident reporting

---

## Lab Architecture

| Component        | OS/Tool           | IP Address      | Role                   |
|------------------|-------------------|-----------------|------------------------|
| Attacker Machine | Kali Linux 2024   | 192.168.56.11   | Attack (Hydra, Nmap)   |
| Target/SIEM      | Ubuntu 24.04 LTS  | 192.168.56.10   | ELK, SSH target        |
| Network          | VirtualBox Host-Only | 192.168.56.0/24 | Lab network            |

---

## Technology Stack

- **Elasticsearch 8.19.7**  
- **Logstash 8.19.7**  
- **Kibana 8.19.7**  
- **Filebeat 8.19.7**  
- **OpenSSH Server**  
- **Hydra 9.5**  
- **Nmap 7.95**

---

## Setup & Configuration

**Elasticsearch JVM Settings:**
-Xms2g
-Xmx2g

text

**Enable SSH:**
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh

text

**Network:**
- VirtualBox Host-Only Adapter (vboxnet0)
- `sudo ufw allow 22`
- `sudo ufw disable` (lab only)

**Filebeat** configured to monitor `/var/log/auth.log` into ELK.

---

## Attack Simulation

### Recon

ping 192.168.56.10
nmap -Pn -p 22 192.168.56.10
ssh root@192.168.56.10

text

### Brute-Force

hydra -t 1 -l root -P /usr/share/wordlists/rockyou.txt ssh://192.168.56.10

text

---

## Detection & Evidence

**/var/log/auth.log:**
Failed password for root from 192.168.56.11 port 39648 ssh2
error: maximum authentication attempts exceeded for root from 192.168.56.11 port 39648 ssh2 [preauth]
Disconnecting authenticating user root 192.168.56.11 port 39648: Too many authentication failures [preauth]
PAM 5 more authentication failures; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.56.11

text

**Indicators:**
- Source IP: 192.168.56.11
- Account: root
- Pattern: High-frequency failed logins

---

## Incident Response

**Actions Taken:**
- Identified attacker IP
- Confirmed no successful logins
- Monitored resources and log integrity
- Forwarded events to ELK/Kibana

**Defensive Recommendations:**
- Implement Fail2ban
- Disable root SSH login (`PermitRootLogin no`)
- Use SSH keys and disable passwords
- Limit login attempts (`MaxStartups`)
- Restrict SSH access by IP
- Enable MFA for remote access

---

## Troubleshooting & Solutions

**Challenge:**  
- Elasticsearch OOM error — set JVM heap to 2GB  
- SSH filtering after brute-force — restart SSH service, reduce attack speed  
- No route to host — full VM shutdown/start, verify adapters/network

---

## Key Learnings

- Resource management in VMs
- Layered network/service troubleshooting
- Impact of attacks on services in test labs
- The value of real-time log analysis

---

## Project Files

project/
├── config/
│ ├── elasticsearch.yml
│ ├── jvm.options
│ ├── filebeat.yml
│ └── logstash.conf
├── screenshots/
│ ├── nmap.png
│ ├── hydra.png
│ ├── authlog.png
│ └── kibana.png
└── README.md

text

---

## Closing Notes

This lab shows:
- End-to-end attack simulation and detection  
- SIEM integration with ELK Stack  
- Practical troubleshooting and reporting for real-world SOC roles

---

**Author:**  
[Your Name] — Cybersecurity Professional  
[LinkedIn](your-link) | [GitHub](your-link)

---

## References
- [Elastic SIEM docs](https://www.elastic.co/guide/en/siem/current/index.html)
- [MITRE ATT&CK: Brute Force (T1110)](https://attack.mitre.org/techniques/T1110/)
- [Hydra](https://github.com/vanhauser-thc/thc-hydra)
