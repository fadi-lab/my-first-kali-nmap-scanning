Nmap scan automation & parsing utilities for Kali Linux (ethical use only).
Parameterized Bash wrapper for Nmap with profiles: quick, full, stealth, and custom flags.
Timestamped outputs in .xml, .gnmap, and .nmap formats (via nmap -oA).
Small Python XML parser to extract host and open-port summaries.
Lint-only CI (shellcheck + flake8) — no scans run inside Actions.
Clear legal/ethical guidance and .gitignore to avoid committing sensitive outputs.\
#clone git clone https://github.com/<YOUR_USERNAME>/kali-nmap-scans.git
cd kali-nmap-scans # make the script executable
chmod +x scripts/nmap_scan.sh # run a safe quick scan (replace <target> with allowed IP/host)
sudo ./scripts/nmap_scan.sh -t <target> -s quick -o scans
