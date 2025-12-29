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


