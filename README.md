# my-first-kali-nmap-scanning
kali-nmap-scans/
├── .gitignore
├── LICENSE
├── README.md
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── scripts/
│   ├── nmap_scan.sh
│   └── parse_nmap_xml.py
├── scans/
│   └── (output stored here)
└── .github/
    └── workflows/
        └── lint.yml
# Nmap scan output
scans/
*.gnmap
*.nmap
*.xml

# Python
__pycache__/
*.pyc
.env
#!/usr/bin/env bash
# Simple, safe nmap wrapper
# Usage: sudo ./nmap_scan.sh -t <target> [-p <ports>] [-o <outdir>] [-s <profile>]
# Example: sudo ./nmap_scan.sh -t 192.0.2.1 -p 1-1000 -o scans -s quick

set -euo pipefail

TARGET=""
PORTS="top-ports"
OUTDIR="scans"
PROFILE="default"

print_usage() {
  cat <<EOF
Usage: sudo $0 -t <target> [-p <ports>] [-o <outdir>] [-s <profile>]

Options:
  -t target      Target host or network (required)
  -p ports       Ports to scan (e.g. 1-1000, 22,80,443). Default: top-ports
  -o outdir      Output directory. Default: scans
  -s profile     Scan profile: quick, full, stealth, default. Default: default

Examples:
  sudo $0 -t 192.0.2.1 -p 1-1000 -o scans -s quick
EOF
}

while getopts "t:p:o:s:h" opt; do
  case ${opt} in
    t) TARGET="$OPTARG" ;;
    p) PORTS="$OPTARG" ;;
    o) OUTDIR="$OPTARG" ;;
    s) PROFILE="$OPTARG" ;;
    h) print_usage; exit 0 ;;
    *) print_usage; exit 1 ;;
  esac
done

if [[ -z "$TARGET" ]]; then
  echo "ERROR: target required."
  print_usage
  exit 2
fi

mkdir -p "$OUTDIR"
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M-%S")
BASE="${TIMESTAMP}_target-$(echo $TARGET | tr '/' '-')"

XML="${OUTDIR}/${BASE}.xml"
NMAPOUT="${OUTDIR}/${BASE}.nmap"
GNMAP="${OUTDIR}/${BASE}.gnmap"

# Select flags by profile
case "$PROFILE" in
  quick)
    FLAGS="-sS -Pn -T4 --top-ports 100"
    ;;
  full)
    FLAGS="-sS -sV -A -O -p- -T3"
    ;;
  stealth)
    FLAGS="-sS -sV -p $PORTS -T2"
    ;;
  default)
    FLAGS="-sS -sV -p $PORTS -T4"
    ;;
  *)
    FLAGS="$PROFILE"  # allow user to pass raw flags
    ;;
esac

echo "Running nmap on ${TARGET} with flags: ${FLAGS}"
echo "Outputs: $XML $NMAPOUT $GNMAP"

# Run nmap
nmap $FLAGS -oA "${OUTDIR}/${BASE}" "$TARGET"

echo "Scan completed."
