### OSCP Kali VM Setup Checklist

## Phase 1: Initial Kali VM Setup
- [ ] sudo apt update
- [ ] sudo apt upgrade -y
- [ ] sudo apt dist-upgrade -y

## Phase 2: Essential Tool Verification
# Create a verification script
cat > ~/check_tools.sh << 'EOF'
#!/bin/bash
echo "=== Essential OSCP Tools Check ==="

TOOLS=(
    "nmap" "gobuster" "dirb" "nikto"
    "hydra" "john" "hashcat" "sqlmap"
    "msfconsole" "searchsploit"
    "python3" "python2" "ruby" "perl"
    "netcat" "socat" "ncat"
    "smbclient" "enum4linux" "impacket-scripts"
    "bloodhound" "responder" "evil-winrm"
    "seclists" "rockyou.txt"
)

for tool in "${TOOLS[@]}"; do
    if which $tool >/dev/null 2>&1 || [ -f "/usr/share/wordlists/$tool" ]; then
        echo "✓ $tool"
    else
        echo "✗ $tool"
    fi
done

echo ""
echo "=== Wordlist Check ==="
ls -la /usr/share/wordlists/rockyou.txt 2>/dev/null || echo "rockyou.txt missing!"
ls -la /usr/share/seclists/ 2>/dev/null || echo "seclists missing!"
EOF

chmod +x ~/check_tools.sh
./check_tools.sh

## Phase 3: Install Missing Essentials
A. Wordlists (CRITICAL for OSCP)
- [ ] sudo apt install -y seclists
- [ ] sudo gunzip /usr/share/wordlists/rockyou.txt.gz 2>/dev/null
- [ ] ls -lh /usr/share/wordlists/rockyou.txt (Should be ~134MB)

B. Additional Useful Tools
# Tools that might be missing
- [ ] sudo apt install -y \
    enum4linux \
    smbmap \
    crackmapexec \
    evil-winrm \
    powershell-empire \
    nessus \
    gobuster \
    ffuf \
    wfuzz \
    sqlmap \
    john \
    hashcat \
    steghide \
    exiftool \
    binwalk \
    strings

## Phase 4: Create Your OSCP Directory Structure
Create the standard OSCP exam structure
- [ ] mkdir -p ~/oscp/{machines,notes,scans,loot,proof,scripts,templates,wordlists}

Subdirectories for organization
- [ ] mkdir -p ~/oscp/wordlists/{quick,medium,custom,targeted}
- [ ] mkdir -p ~/oscp/notes/{methodology,commands,references}
- [ ] mkdir -p ~/oscp/scripts/{recon,privesc,enum,post}
- [ ] mkdir -p ~/screenshots

## Phase 5: Create Custom Wordlists (Your Secret Weapon)
Quick Attack Lists:
# 1. Ultra-quick passwords (first attempt)
- [ ] cat > ~/oscp/wordlists/quick/passwords_top20.txt << 'EOF'

password
123456
admin
root
12345
1234
123
admin123
password123
qwerty
test
welcome
monkey
letmein
dragon
baseball
football
mustang
master
michael
shadow
EOF

# 2. Quick usernames
- [ ] cat > ~/oscp/wordlists/quick/usernames.txt << 'EOF'
root
admin
administrator
user
test
guest
administrateur
adm
mysql
oracle
postgres
ftp
ssh
www-data
EOF

# 3. Quick directories
- [ ] cat > ~/oscp/wordlists/quick/directories.txt << 'EOF'
/
/admin
/phpmyadmin
/backup
/log
/test
/dev
/secret
/hidden
/private
/secure
EOF

Medium Lists (If quick fails):
Copy first 1000 from rockyou
- [ ] head -1000 /usr/share/wordlists/rockyou.txt > ~/oscp/wordlists/medium/passwords_top1000.txt

Common directories medium list
- [ ] cp /usr/share/seclists/Discovery/Web-Content/common.txt ~/oscp/wordlists/medium/directories_common.txt

## Phase 6: Essential OSCP Scripts
A. Quick Recon Script:
cat > ~/oscp/scripts/recon/quick_scan.sh << 'EOF'
#!/bin/bash
# Quick recon for OSCP exam
TARGET=$1
OUTDIR=~/oscp/scans

if [ -z "$TARGET" ]; then
    echo "Usage: $0 <target_ip>"
    exit 1
fi

echo "=== Scanning $TARGET ==="
mkdir -p $OUTDIR

# Quick port scan
echo "[+] Quick port scan..."
nmap -p- --min-rate=1000 -T4 $TARGET -oN $OUTDIR/${TARGET}_quick.txt

# Service scan on open ports
echo "[+] Service scan..."
ports=$(grep "^[0-9]" $OUTDIR/${TARGET}_quick.txt | cut -d'/' -f1 | tr '\n' ',')
nmap -sC -sV -p${ports} $TARGET -oN $OUTDIR/${TARGET}_services.txt

# UDP top 100
echo "[+] UDP scan (top 100)..."
nmap -sU --top-ports=100 $TARGET -oN $OUTDIR/${TARGET}_udp.txt

echo "[+] Done! Results in $OUTDIR/"
EOF

chmod +x ~/oscp/scripts/recon/quick_scan.sh

B. Auto-Enumeration Script:
cat > ~/oscp/scripts/enum/auto_enum.sh << 'EOF'
#!/bin/bash
# Auto-enumeration based on open ports
TARGET=$1

if [ -z "$TARGET" ]; then
    echo "Usage: $0 <target_ip>"
    exit 1
fi

echo "=== Auto-Enumeration for $TARGET ==="

# Check HTTP/HTTPS
if nmap -p 80 $TARGET | grep -q "open"; then
    echo "[+] HTTP (80) found"
    echo "    Directory brute: gobuster dir -u http://$TARGET -w ~/oscp/wordlists/quick/directories.txt"
fi

if nmap -p 443 $TARGET | grep -q "open"; then
    echo "[+] HTTPS (443) found"
    echo "    Check SSL: openssl s_client -connect $TARGET:443 -showcerts"
fi

if nmap -p 139,445 $TARGET | grep -q "open"; then
    echo "[+] SMB (139/445) found"
    echo "    Enum: enum4linux -a $TARGET"
    echo "    Shares: smbclient -L //$TARGET/ -N"
fi

if nmap -p 22 $TARGET | grep -q "open"; then
    echo "[+] SSH (22) found"
    echo "    Version: nc -nv $TARGET 22"
    echo "    Brute: hydra -l root -P ~/oscp/wordlists/quick/passwords_top20.txt ssh://$TARGET"
fi
EOF

chmod +x ~/oscp/scripts/enum/auto_enum.sh

# Phase 7: Note Templates & Documentation
A. Machine Notes Template:
cat > ~/oscp/templates/machine_template.md << 'EOF'
# Machine: [IP]
# Points: [25/20/20/25/40]
# Difficulty: [Easy/Medium/Hard]

## Initial Recon
### Nmap Results
```bash
```



Enumeration Findings
Web (Port 80/443)
Directory brute results

Technologies found

Interesting pages

SMB (Port 139/445)
Shares found

Null session access

Users enumerated

Other Services
SSH version/access

FTP access

Database services

Vulnerability Identification
Potential Vulnerabilities
[Service] [Version] - [CVE/Exploit]

[Service] [Version] - [CVE/Exploit]

Exploitation
Steps to Initial Access
Proof of User Access
Command: whoami

Output:

Screenshot: [filename.png]

Privilege Escalation
Enumeration Results
Kernel version:

SUID binaries:

Cron jobs:

Sudo permissions:

Escalation Path
Proof of Root Access
Command: id

Output:

Screenshot: [filename.png]

Flags Captured
user.txt: [content/redacted]

root.txt: [content/redacted]

Lessons Learned
What worked:

What didn't:

Time spent: [hours]
EOF
























