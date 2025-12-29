# commands/nmap.md
## Nmap Cheatsheet

### Quick Scans
- Basic: `nmap -sC -sV 10.11.1.5`
- Full ports: `nmap -p- --min-rate=1000 10.11.1.5`
- UDP top 100: `nmap -sU --top-ports=100 10.11.1.5`

### Service Detection
- Version: `nmap -sV 10.11.1.5`
- OS detection: `nmap -O 10.11.1.5`
- Scripts: `nmap --script vuln 10.11.1.5`
