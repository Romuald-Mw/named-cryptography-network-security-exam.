# Integrated Situation Final Practical Project

**Module:** Cryptography & Network Security (ETTCS801)  
**Institution:** ULK Polytechnic Institute   

---

## Project Overview

This repository contains the practical security implementation and technical documentation addressing key vulnerabilities in student record management, host isolation, and access control.

---

## Project Structure

```text
.
├── security_toolkit.py                     # Python utility for AES/Fernet encryption/decryption & SHA-256 hashing
├── risk_assessment.md                      # Risk analysis, asset mapping, and priority ranking
├── filter_tests.md                         # iptables rule configuration and netcat empirical test logs
├── cryptography_network_security_exam.tex  # Main technical report source (LaTeX format)
├── cryptography_network_security_exam.pdf  # Compiled final technical report
└── README.md                               # Repository documentation and user guide
```

### 1. Cryptographic Toolkit (`security_toolkit.py`)

Run the Python utility using command-line arguments:

```powershell
# Display help and available options
python security_toolkit.py -h

# Encrypt a target file
python security_toolkit.py --encrypt sample.txt

# Decrypt an encrypted file
python security_toolkit.py --decrypt sample.txt.enc

# Generate SHA-256 integrity hash
python security_toolkit.py --hash sample.txt

# Verify file integrity against an expected hash
python security_toolkit.py --verify sample.txt <EXPECTED_HASH>
```

### 2. Network Traffic Filtering (`filter_tests.md`)

Default-deny `iptables` firewall on the central server (`192.168.50.100`): the Staff subnet (`192.168.50.0/24`) can reach HTTP on port 80, the Guest subnet (`192.168.60.0/24`) is blocked.

```bash
# Flush existing rules and set default-deny policy
sudo iptables -F
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP

# Allow loopback and established connections
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Drop all traffic from Guest network
sudo iptables -A INPUT -s 192.168.60.0/24 -j DROP

# Permit HTTP for Staff network
sudo iptables -A INPUT -s 192.168.50.0/24 -p tcp --dport 80 -j ACCEPT

# Drop all remaining port 80 traffic
sudo iptables -A INPUT -p tcp --dport 80 -j DROP
```

Validate the rules with `netcat`:

```bash
# Staff -> HTTP (expected: succeeds)
nc -zv -s 192.168.50.25 192.168.50.100 80

# Guest -> HTTP (expected: timeout)
nc -zv -w 3 -s 192.168.60.77 192.168.50.100 80

# Staff -> SSH (expected: timeout, blocked by default policy)
nc -zv -w 3 -s 192.168.50.25 192.168.50.100 22
```
