# Cloud Security Operations & Cryptography Fundamentals

**Project Author:** Oluwasheyi Olayemi Ojelade
**Status:** ✅ Completed / Documented
**Focus:** **Cloud Hardening, Change Management, Cryptography, and Encryption Protocols.**

---

## 📌 Part 1: Cloud Security Operations & Hardening

### 1.1 Eradicating Default Configurations

- When deploying new cloud resources or hardware (like Cisco routers), they often start with permissive or default configurations.
- For instance, many enterprise routers come with default credentials like `admin/admin` or `cisco/cisco`.
- Leaving these defaults unchanged is a poor security practice, as threat actors specifically search for and exploit these known credentials first.
- Hardening requires moving away from default VPCs and default Security Groups.
- A Security Group should not leave ports open (like Port 22 for SSH) unless it is strictly necessary.

### 1.2 The "Window of Opportunity" & Patch Management

- Patching involves updating packages to fix known vulnerabilities (e.g., running `apt-get update` or `yum update`).
- Attackers exploit the "Window of Opportunity"—the timeframe between when a vulnerability is identified and when the system is actually patched.
- This window can last months or even a year if patching is neglected.
- For large environments, manual patching is impossible; organizations must use automation tools like AWS Systems Manager Patch Manager or Azure Update Management.

### 1.3 Utilizing CIS Benchmarks for Standardization

- To establish a minimum security baseline, organizations rely on the Center for Internet Security (CIS) Benchmarks.
- CIS Benchmarks provide a consensus-based set of best practices for securely configuring IT systems, software, and cloud infrastructure.
- For example, the CIS AWS Foundations Benchmark dictates specific rules, such as ensuring the "default" security group of every VPC restricts all traffic.

### 1.4 Pre-Deployment Vulnerability Scanning

- Before moving resources into production, pre-deployment scanning is mandatory.
- For Linux EC2 instances, tools like OpenVAS or Nessus are used to identify underlying OS vulnerabilities.
- For lightweight Docker containers, tools like Trivy or Checkmarx are utilized to scan container images and code dependencies.

---

## 📌 Part 2: Change Management in Cloud Environments

### 2.1 The Dangers of Unmanaged Changes

- Making infrastructure changes directly via the UI without documentation makes troubleshooting and auditing incredibly difficult.
- "Configuration Drift" occurs when unauthorized or undocumented changes deviate a system from its established baseline.
- Continuous monitoring tools (like Datadog, Prometheus, or AWS Config) are essential to detect and alert on these drifts.

### 2.2 The Change Management Workflow

- **Formal Change Request (CR):** Any proposed change must be documented, including the target configuration items, justification, step-by-step implementation plan, verification methods, and a contingency (rollback) plan.
- **Change Advisory Board (CAB):** Changes are reviewed and approved by a cross-functional board (including security and compliance teams) to evaluate potential risks and impacts.
- **Change Windows & Testing:** Changes must be tested in a staging environment first. Approved changes are then scheduled during "Change Windows"—typically off-peak hours (e.g., 2:00 AM) to minimize business impact in case of failure.

---

## 📌 Part 3: The Evolution of Cryptography

### 3.1 Core Definitions

- **Cryptology:** The overarching science focused on secure communication.
- **Cryptography:** The specific practice of transforming (encrypting) messages to conceal their meaning using algorithms.
- **Cryptanalysis:** The discipline dedicated to deciphering, breaking, or hacking encrypted messages back into plain text without the key.

### 3.2 The Julius Caesar Cipher (The Origin of Hacking)

- Historically, militaries needed to send intelligence securely without messengers intercepting the contents.
- Julius Caesar utilized a "Substitution Cipher" that shifted alphabet letters by a specific number (a key).
- For example, using a shift key of 3, the word "HELLO" becomes "KHOOR" (dictated as "Kho" in the session) by shifting each letter forward by three places.
- **The Vulnerability:** Because the English alphabet only has 26 letters, there are only 26 possible combinations. Early hackers (interceptors) could easily brute-force the message by trying all 26 shifts until the message made sense.

---

## 📌 Part 4: Modern Encryption: Symmetric vs. Asymmetric

To handle massive amounts of digital data securely, modern mathematics introduced advanced algorithms.

### 4.1 Symmetric Encryption (Shared Key)

- **Mechanism:** Both the sender and the receiver use the exact same key to encrypt and decrypt the data.
- **Algorithms:** AES (Advanced Encryption Standard), DES, 3DES.
- **Pros:** Highly efficient, fast, and uses less computational resources, making it ideal for bulk data transfer.
- **Cons (The Risk):** Key Distribution. Both parties must securely exchange the key; if an attacker intercepts this single key, the entire communication is compromised.

### 4.2 Asymmetric Encryption (Public/Private Key Pair)

- **Mechanism:** Uses a mathematically linked pair of keys.
    - **Public Key:** Shared openly with the world. Anyone can use it to encrypt a message sent to the owner.
    - **Private Key:** Kept strictly secret by the owner. It is the only key capable of decrypting the messages encrypted by the Public Key.
- **Algorithms:** RSA, ECC.
- **Pros:** Solves the key distribution problem since the decryption key (Private Key) is never shared over the network.
- **Cons:** Extremely slow and computationally expensive, making it unsuited for large data streams.

### 4.3 The Hybrid Approach (TLS Protocol)

- Modern secure web browsing (HTTPS) uses Transport Layer Security (TLS), which combines both methods.
- **Step 1:** The server and client use Asymmetric Encryption solely to securely exchange a temporary Symmetric key.
- **Step 2:** Once the Symmetric key is safely shared, the resource-heavy Asymmetric encryption is dropped, and the rest of the communication continues using the fast Symmetric encryption.

---

## 📌 Part 5: Cryptography in the Cloud

### 5.1 Securing Data at Rest

- By default, when you store data in AWS S3, it is encrypted using AWS Managed Keys.
- AWS handles the cryptographic rotation of these keys automatically.
- **Compliance Overrides:** Strict federal or defense compliance frameworks may not trust keys managed by the Cloud Provider. In these cases, engineers use AWS KMS (Key Management Service) to implement Customer Managed Keys.
- For the highest level of security, organizations can use a Hardware Security Module (HSM) to keep the encryption keys physically outside of the AWS environment.

### 5.2 Securing Data in Transit

- Internal service-to-service communication within the AWS cloud utilizes HTTPS and TLS to ensure all moving data remains encrypted.

[🛡️Enterprise Cloud Security & Vulnerability Management: Practical Lab](Cloud%20Security%20Operations%20&%20Cryptography%20Fundament/%F0%9F%9B%A1%EF%B8%8FEnterprise%20Cloud%20Security%20&%20Vulnerability%20Manage%20323d65318cf6804fbf2ac6a5f08987ac.md)