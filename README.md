# Linux-1

# Linux Lab 1 – SSH Brute Force and Privilege Escalation

## Project Overview

This project is part of a four-machine vulnerable lab environment designed to simulate a small company network for offensive security practice.  
Linux Lab 1 represents an externally exposed Linux server with a misconfigured SSH service that allows credential brute-force attacks, leading to initial access and subsequent privilege escalation due to improper file permissions.

The lab is designed to be solved in a black-box penetration testing scenario and focuses on realistic misconfigurations commonly found in enterprise environments.

---
## Lab Links

- Download Linux-1 : https://mega.nz/file/TRQmGJIC#syfm00N_z1xcUVrsYSTzyW3Qv0fc5CRCW8UDLKvysKM
- Documentation for Linux-1 With Walkthrough: https://drive.google.com/file/d/1sEMaauP_jIeu_CvYPWvRZ7Q4vXVPUKfD/view?usp=drive_link
---

## Lab Objectives

- Perform service enumeration against a Linux target
- Identify exposed SSH service
- Discover leaked service account information
- Execute SSH brute-force attack
- Gain initial user-level access
- Perform privilege escalation due to misconfigured system files
- Capture user and root flags

---

## Vulnerability Summary

- **Attack Type:** Credential Brute Force
- **Initial Access Vector:** SSH service
- **Privilege Escalation Vector:** Writable `/etc/shadow` file
- **Testing Methodology:** Black-box penetration testing

---

## Impact Description

A successful brute-force attack against the SSH service allows an attacker to gain access to a valid service account.  
Due to improper permission settings on critical authentication files, the attacker can modify password hashes and escalate privileges to the root user.

### Security Impact
- Unauthorized system access
- Full root compromise
- Complete loss of system confidentiality, integrity, and availability
- Potential lateral movement to internal systems

---

## Exploitable Operating System

- **Target OS:** Ubuntu Server 20.04 LTS (CLI-only)
- **Architecture:** x86_64
- **Hardening Level:** Minimal (intentional misconfiguration)

---

## Exploited Service Details

- **Service Name:** OpenSSH
- **Port:** 22/TCP
- **Authentication Method:** Password-based authentication
- **Misconfiguration:**
  - Weak service account credentials
  - SSH login banner leaking service account name
  - Root SSH login disabled (intentionally)
  - Writable `/etc/shadow` file

---

## Attack Flow

- Port scanning and service enumeration
- SSH banner enumeration reveals service account name
- SSH brute-force attack against service account
- Successful SSH login as low-privileged user
- Enumeration of file permissions
- Password hash manipulation via `/etc/shadow`
- Root privilege escalation
- Flag capture

---

## Flags

- **User Flag Location:** `/home/svcuser/user.txt`
- **Root Flag Location:** `/root/root.txt`

---

## Minimum System Requirements

### Attacker Machine
- Kali Linux (recommended)
- 2 GB RAM
- 1 CPU core
- 20 GB disk space
- Tools:
  - Nmap
  - Hydra
  - OpenSSH client

### Vulnerable Machine
- Ubuntu Server 20.04 LTS
- 2 GB RAM
- 1 CPU core
- 20 GB disk space
- VMware Workstation or VirtualBox

### Network
- Host-only or NAT network
- Direct connectivity between attacker and target

---

## Lab Creation Steps (Reproducible)

Follow the steps below to recreate the Linux Lab 1 machine.

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install SSH service
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh

# Create service account
sudo useradd -m svcuser
sudo passwd svcuser

# Disable root SSH login
sudo nano /etc/ssh/sshd_config
PermitRootLogin no
PasswordAuthentication yes

# Configure SSH banner for username disclosure
sudo nano /etc/issue.net
Authorized access only
Service Account: svcuser

sudo nano /etc/ssh/sshd_config
Banner /etc/issue.net

sudo systemctl restart ssh

# Introduce privilege escalation vulnerability
sudo chmod 666 /etc/shadow

# Remove unintended privileges
sudo deluser svcuser sudo
sudo deluser svcuser lxd

# Create user flag
echo "user_flag{ssh_bruteforce_success}" | sudo tee /home/svcuser/user.txt
sudo chown svcuser:svcuser /home/svcuser/user.txt
sudo chmod 644 /home/svcuser/user.txt

# Create root flag
sudo bash -c 'echo "root_flag{shadow_file_misconfiguration}" > /root/root.txt'
sudo chmod 600 /root/root.txt

```
## Learning Outcomes

- Understanding real-world SSH misconfigurations

- Practical experience with credential brute-force attacks

- Importance of secure file permissions

- Linux privilege escalation fundamentals

- Black-box penetration testing methodology

## Disclaimer

This lab is created strictly for educational and ethical penetration testing purposes.
Do not deploy these configurations in production environments.

## Created By

- Name: Gopesh Kachhadiya
- Role: Junior Penetration Tester 
- Purpose: Internship Project and Skill Demonstration
- Domain: Offensive Security and AI Penetration Testing
- This lab was created as part of an offensive cybersecurity internship project focused on realistic attack simulations and hands-on learning.
