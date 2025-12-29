## Your OSCP Exam Day Command Sheet:
# OSCP Exam Quick Reference
## Initial Setup
- [ ] sudo openvpn exam.ovpn
- [ ] ifconfig tun0
- [ ] ./quick_scan.sh 10.11.1.5

## Common Commands
nmap -sC -sV -oA scan 10.11.1.5
gobuster dir -u http://10.11.1.5 -w ~/oscp/wordlists/quick/directories.txt
hydra -l admin -P ~/oscp/wordlists/quick/passwords_top20.txt ssh://10.11.1.5

## Documentation
./screenshot.sh
notes  # Opens daily notes

## What Kinds of Scripts Are Perfectly Legal:
#✅ Type 1: Recon/Enumeration Scripts
# Example: quick_scan.sh (TOTALLY ALLOWED)
#!/bin/bash
# This just automates YOUR methodology
nmap -sC -sV $1
gobuster dir -u http://$1 -w common.txt

#✅ Type 2: Note-Taking Scripts
# Example: log_command.sh (ALLOWED)
#!/bin/bash
# Logs your commands with timestamps
echo "$(date): $@" >> ~/oscp/command_log.txt
eval "$@"

#✅ Type 3: Proof Collection Scripts
# Example: get_proof.sh (ALLOWED)
#!/bin/bash
# Collects proof after exploitation
echo "=== Proof Collection ==="
whoami
hostname
ip addr
cat /etc/passwd | tail -5

#✅ Type 4: Productivity Scripts
# Example: setup_target.sh (ALLOWED)
#!/bin/bash
# Sets up directories for a new target
mkdir -p ~/oscp/machines/$1/{scans,loot,proof}
cp ~/oscp/templates/machine.md ~/oscp/notes/$1.md

## 📁 How to Bring Scripts Into Exam VM:
Method 1: Pre-load into OffSec Kali VM
During PEN-100 course, add scripts to their VM
Save in: ~/oscp/scripts/
Take snapshot with scripts included

