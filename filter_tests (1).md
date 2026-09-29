# Network Traffic Filtering Configuration & Execution Log

## 1. Target Topology & Environment
* **Central Server IP:** `192.168.50.100`
* **Staff Subnet:** `192.168.50.0/24` (Authorized for Port 80 HTTP access)
* **Guest Subnet:** `192.168.60.0/24` (Untrusted; must be blocked)
* **Testing Machine:** Linux Client VM (`faith-Virtual-Machine`)

---

## 2. Firewall Rule Architecture (`iptables`)

```bash
# Flush existing rules and set default-deny policy
sudo iptables -F
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP

# Allow loopback and established connections
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Explicitly drop all ingress traffic from Guest network
sudo iptables -A INPUT -s 192.168.60.0/24 -j DROP

# Permit HTTP access specifically for Staff network
sudo iptables -A INPUT -s 192.168.50.0/24 -p tcp --dport 80 -j ACCEPT

# Drop all remaining port 80 traffic
sudo iptables -A INPUT -p tcp --dport 80 -j DROP
```

---

## 3. Empirical Test Execution Log

### Test 1: Permitted Access — Staff Subnet on Port 80 (HTTP)
* **Command Executed:**

```bash
  nc -zv -s 192.168.50.25 192.168.50.100 80
```

* **Expected Outcome:** Successful TCP three-way handshake and connection established.
* **Actual Output:**

```
  Connection to 192.168.50.100 80 port [tcp/http] succeeded!
```

* **Verdict:** PASS (Explicitly allowed by Staff subnet rule).

### Test 2: Blocked Access 1 — Guest Subnet Isolation on Port 80
* **Command Executed:**

```bash
  nc -zv -w 3 -s 192.168.60.77 192.168.50.100 80
```

* **Expected Outcome:** Connection request silently dropped, resulting in a timeout without response.
* **Actual Output:**

```
  nc: connect to 192.168.50.100 port 80 (tcp) timed out: Operation in progress
```

* **Verdict:** PASS (Blocked by `-s 192.168.60.0/24 -j DROP` rule).

### Test 3: Blocked Access 2 — Unauthorized Service Port (SSH Port 22)
* **Command Executed:**

```bash
  nc -zv -w 3 -s 192.168.50.25 192.168.50.100 22
```

* **Expected Outcome:** Connection attempt blocked by default-deny ingress policy.
* **Actual Output:**

```
  nc: connect to 192.168.50.100 port 22 (tcp) timed out: Operation in progress
```

* **Verdict:** PASS (Blocked by default INPUT DROP policy).