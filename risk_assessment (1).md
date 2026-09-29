# Integrated Risk Assessment & Defense Strategy

## 1. Asset and Vulnerability Identification

### Asset 1: Student Records Database & Archives
* **Identified Vulnerability:** Unencrypted file storage and unencrypted cross-campus file transfers.
* **Potential Consequences:** Interception, unauthorized disclosure, and tampering of sensitive academic records, leading to severe data privacy violations.

### Asset 2: Central Server Infrastructure
* **Identified Vulnerability:** Direct Guest network access allowed to internal server subnets without firewall isolation.
* **Potential Consequences:** Lateral network discovery, unauthorized probing, service abuse, and potential Denial of Service (DoS) attacks from untrusted devices.

### Asset 3: System Administrator & Staff User Accounts
* **Identified Vulnerability:** Weak password practices and outdated software on host operating systems.
* **Potential Consequences:** Credential brute-forcing, unauthorized administrative privilege escalation, and full system takeover.

---

## 2. Risk Evaluation & Priority Ranking

| Risk ID | Risk Description | Likelihood | Impact | Priority Rank |
| :--- | :--- | :--- | :--- | :--- |
| **R1** | **Unencrypted Data Storage & Transmission** | High | High | **Critical (Rank 1)** |
| **R2** | **Direct Guest Network Access to Central Server** | High | Medium | **High (Rank 2)** |
| **R3** | **Weak Staff Passwords & Credential Compromise** | Medium | High | **Medium (Rank 3)** |

### Ranking Justifications:
* **Rank 1 (Unencrypted Data):** Rated Critical because unencrypted transfers across public/shared campus links happen routinely. Interception directly damages data confidentiality and institutional integrity.
* **Rank 2 (Guest Access):** Rated High due to direct network exposure without filtering boundaries, allowing unauthenticated guest devices to probe server ports.
* **Rank 3 (Weak Passwords):** Rated Medium because exploitation requires targeted brute-force activity or active social engineering, making it slightly less immediate than cleartext network transfers.

---

## 3. Recommended Security Controls

1. **Data Confidentiality & Integrity Control:** Enforce symmetric encryption (AES-128/256 via Fernet) for files at rest and in transit, verified via SHA-256 checksums.
2. **Network Traffic Isolation Control:** Configure host firewall filtering (`iptables`) to explicitly drop incoming packets originating from the Guest network (`192.168.20.0/24`).
3. **Access Control & Identity Management:** Implement enforced multi-factor authentication (MFA), strong password complexity rules, and automated OS security patch updates.