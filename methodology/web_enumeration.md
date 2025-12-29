# methodology/web_enumeration.md
## Web Enumeration Step-by-Step

### Step 1: Initial Check
1. `curl -I http://target/`
2. `whatweb http://target/`
3. Check page source for comments

### Step 2: Directory Brute Force
1. Quick: `gobuster dir -u http://target -w quick_dirs.txt`
2. Full: `gobuster dir -u http://target -w big.txt -x php,html,txt`

### Step 3: Vulnerability Check
1. `nikto -h http://target`
2. Check for default credentials
3. Look for outdated software
