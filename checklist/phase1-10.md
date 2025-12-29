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


