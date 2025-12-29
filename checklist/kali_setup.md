Phase 1: Initial Scans (Hour 0-1)
# Create target list
TARGETS="10.11.1.5 10.11.1.10 10.11.1.15 10.11.1.20 10.11.1.100 10.11.1.110 10.11.1.120"

# Quick alive check on ALL targets
for ip in $TARGETS; do
    ping -c 1 -W 1 $ip && echo "$ip is alive"
done

# Basic service scan on all (parallel)
for ip in $TARGETS; do
    nmap -sC -sV -oA ~/oscp/scans/initial_$ip $ip &
done


Phase 2: Focused Enumeration
# Pick easiest target first (usually 25-pointer)
# Do FULL enumeration on that ONE machine
nmap -p- --min-rate=1000 -T4 10.11.1.5 -oN full_ports.txt

# Then service scan on open ports
ports=$(grep "^[0-9]" full_ports.txt | cut -d'/' -f1 | tr '\n' ',')
nmap -sC -sV -p$ports 10.11.1.5 -oN services.txt

Solution: Be precise with your scans
# GOOD: Only scan provided IPs
TARGETS="10.11.1.5 10.11.1.10 10.11.1.15 10.11.1.20"
for ip in $TARGETS; do
    ping -c 1 $ip  # ONLY provided IPs
done

🔧 Safe Scanning Script for OSCP:
cat > ~/oscp/scripts/safe_scan.sh << 'EOF'
#!/bin/bash
# OSCP-safe scanner - only scans provided targets

TARGET_FILE=~/oscp/targets.txt
SCAN_DIR=~/oscp/scans

if [ ! -f "$TARGET_FILE" ]; then
    echo "ERROR: Create $TARGET_FILE first with target IPs"
    echo "Example:"
    echo "10.11.1.5"
    echo "10.11.1.10"
    exit 1
fi

echo "=== OSCP Safe Scan ==="
echo "Targets: $(cat $TARGET_FILE | tr '\n' ' ')"

mkdir -p $SCAN_DIR

while read -r target; do
    if [ -n "$target" ]; then
        echo "[+] Scanning $target..."
        nmap -sC -sV -oA $SCAN_DIR/initial_$target $target
    fi
done < "$TARGET_FILE"

echo "[+] All scans completed safely"
EOF

chmod +x ~/oscp/scripts/safe_scan.sh

Good Practice:
# KNOW your target IP (e.g., from platform)
TARGET=10.10.10.100

# Scan ONLY that IP
nmap -sC -sV $TARGET

📊 OSCP Exam Network Layout:
Your Kali VM (tun0: 10.11.1.x)
       │
       VPN
       │
       ├── 10.11.1.5    (25 pts)
       ├── 10.11.1.10   (20 pts)
       ├── 10.11.1.15   (20 pts)
       ├── 10.11.1.20   (25 pts)
       └── 10.11.1.100-120  (AD set - 40 pts)
           
           NO OTHER HOSTS EXIST!


























