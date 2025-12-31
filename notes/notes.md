
## Your OSCP Exam Day Command Sheet:
# OSCP Exam Quick Reference
# Initial Setup
- [ ] sudo openvpn exam.ovpn
- [ ] ifconfig tun0
- [ ] ./quick_scan.sh 10.11.1.5

# Common Commands
```
nmap -sC -sV -oA scan 10.11.1.5
gobuster dir -u http://10.11.1.5 -w ~/oscp/wordlists/quick/directories.txt
hydra -l admin -P ~/oscp/wordlists/quick/passwords_top20.txt ssh://10.11.1.5
```
# Documentation
./screenshot.sh
notes  # Opens daily notes

# What Kinds of Scripts Are Perfectly Legal:
#✅ Type 1: Recon/Enumeration Scripts
```
# Example: quick_scan.sh (TOTALLY ALLOWED)
#!/bin/bash
# This just automates YOUR methodology
nmap -sC -sV $1
gobuster dir -u http://$1 -w common.txt
```
#✅ Type 2: Note-Taking Scripts
```
# Example: log_command.sh (ALLOWED)
#!/bin/bash
# Logs your commands with timestamps
echo "$(date): $@" >> ~/oscp/command_log.txt
eval "$@"
```
#✅ Type 3: Proof Collection Scripts
```
# Example: get_proof.sh (ALLOWED)
#!/bin/bash
# Collects proof after exploitation
echo "=== Proof Collection ==="
whoami
hostname
ip addr
cat /etc/passwd | tail -5
```
#✅ Type 4: Productivity Scripts
```
# Example: setup_target.sh (ALLOWED)
#!/bin/bash
# Sets up directories for a new target
mkdir -p ~/oscp/machines/$1/{scans,loot,proof}
cp ~/oscp/templates/machine.md ~/oscp/notes/$1.md
```
## 📁 How to Bring Scripts Into Exam VM:
Method 1: Pre-load into OffSec Kali VM
During PEN-100 course, add scripts to their VM
Save in: ~/oscp/scripts/
Take snapshot with scripts included

Method 2: Transfer During Exam
# During exam, you can:
1. Download from your GitHub
```
git clone https://github.com/yourname/oscp-scripts.git
```

2. Copy from host via shared folder
Enable shared folders in VMware, then:
```
cp /mnt/hgfs/oscp_scripts/* ~/oscp/scripts/
```
3. Paste from notes
Have scripts in text format, copy-paste

Method 3: Have in Text Format
Keep scripts in a text file, copy during exam
```
cat > ~/exam_scripts.txt << 'EOF'
#!/bin/bash
# quick_scan.sh
nmap -sC -sV $1

#!/bin/bash  
# screenshot.sh
scrot -s ~/screenshots/$(date +%s).png
EOF
```
#🔧 Essential OSCP Scripts You SHOULD Create:
Script 1: OSCP Exam Starter Kit
```
cat > ~/oscp/scripts/exam_start.sh << 'EOF'
#!/bin/bash
# OSCP Exam Initial Setup
echo "=== OSCP Exam Setup ==="
```

# Create target directories
```
TARGETS="10.11.1.5 10.11.1.10 10.11.1.15 10.11.1.20"
for target in $TARGETS; do
    mkdir -p ~/oscp/machines/$target/{scans,loot,proof}
    echo "Created: ~/oscp/machines/$target/"
done
```
# Start note file
```
echo "# OSCP Exam - $(date)" > ~/oscp/exam_notes.md
echo "Good luck! 🚀" >> ~/oscp/exam_notes.md

echo "Setup complete!"
EOF
```
Script 2: Command Logger (CRITICAL)
```
cat > ~/oscp/scripts/logcmd.sh << 'EOF'
#!/bin/bash
# Logs all commands with timestamp
LOG_FILE=~/oscp/command_log_$(date +%Y%m%d).txt

echo "$(date '+%Y-%m-%d %H:%M:%S') - $USER@$(hostname): $@" >> $LOG_FILE

# Execute the command
eval "$@"
```
# Take screenshot if GUI available
```
if [ -n "$DISPLAY" ]; then
    scrot -s "~/screenshots/$(date +%Y%m%d_%H%M%S).png" 2>/dev/null
fi
EOF
```
# Add alias to .bashrc
```
echo "alias lc='~/oscp/scripts/logcmd.sh'" >> ~/.bashrc
```
Script 3: Proof Collector
```
cat > ~/oscp/scripts/get_proof.sh << 'EOF'
#!/bin/bash
# Collects system proof after exploitation
echo "=== SYSTEM PROOF ==="
echo "Time: $(date)"
echo "User: $(whoami)"
echo "Hostname: $(hostname)"
echo ""
echo "=== NETWORK ==="
ip addr 2>/dev/null || ifconfig 2>/dev/null
echo ""
echo "=== OS INFO ==="
uname -a
cat /etc/*release 2>/dev/null | head -5
echo ""
echo "=== USER INFO ==="
id
sudo -l 2>/dev/null
echo ""
echo "=== PROCESSES ==="
ps aux | head -10
EOF
```
Script 4: Quick Service Checker
```
cat > ~/oscp/scripts/check_services.sh << 'EOF'
#!/bin/bash
# Quick check of common services
TARGET=$1

echo "=== Quick Service Check: $TARGET ==="

# HTTP
echo -n "HTTP (80): "
curl -s -I http://$TARGET:80 2>/dev/null | head -1

# HTTPS
echo -n "HTTPS (443): "
curl -s -I -k https://$TARGET:443 2>/dev/null | head -1

# SSH
echo -n "SSH (22): "
timeout 2 nc -z $TARGET 22 && echo "Open" || echo "Closed/Filtered"

# SMB
echo -n "SMB (445): "
timeout 2 nc -z $TARGET 445 && echo "Open" || echo "Closed/Filtered"
EOF
```
#📦 How to Package Your Scripts for Exam:
Create a Deployment Package:
Create a tar.gz of all your scripts
```
tar -czf oscp_scripts_$(date +%Y%m%d).tar.gz ~/oscp/scripts/
```
# Or create a GitHub repo
```
git init ~/oscp_scripts
cd ~/oscp_scripts
git add .
git commit -m "OSCP exam scripts"
git remote add origin https://github.com/yourname/oscp-scripts.git
git push -u origin main
```

Quick Install Script:
```
cat > install_oscp_scripts.sh << 'EOF'
#!/bin/bash
# Installs all OSCP scripts
echo "Installing OSCP scripts..."

# Create directories
mkdir -p ~/oscp/{scripts,notes,scans}

# Download from GitHub
wget https://raw.githubusercontent.com/yourname/oscp-scripts/main/quick_scan.sh -O ~/oscp/scripts/quick_scan.sh
wget https://raw.githubusercontent.com/yourname/oscp-scripts/main/logcmd.sh -O ~/oscp/scripts/logcmd.sh

# Make executable
chmod +x ~/oscp/scripts/*.sh

echo "Installation complete!"
EOF
```
# ⚠️ Important Rules to Remember:
You Can: Write scripts that help YOUR workflow
Automate repetitive tasks
Create templates and shortcuts
Use publicly available scripts



