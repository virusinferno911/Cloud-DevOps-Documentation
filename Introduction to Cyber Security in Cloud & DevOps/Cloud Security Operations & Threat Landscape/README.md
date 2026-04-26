# Cloud Security Operations & Threat Landscape

**Project Author:** Oluwasheyi Olayemi Ojelade
**Status:** ✅ Completed / Documented
**Focus:** Cloud Attack Surfaces, Advanced Persistent Threats (APTs), Network Attacks (DoS), and Security Auditing.

---

## 📌 Part 1: The Expanding Cloud Attack Surface

As organizations migrate from traditional data centers to the cloud (IaaS, PaaS, SaaS), they inherit flexibility and scalability, but they also expand their **Attack Surface**—the total number of points where an unauthorized user can try to enter data or extract data. Every new service deployed (e.g., AWS EC2, S3, IAM, KMS) introduces potential vulnerabilities if not properly threat-modeled and secured.

### 1.1 The Shared Responsibility Model

Security in the cloud is a partnership.

- **Cloud Service Provider (CSP):** Secures the physical infrastructure (data centers, hardware, networking).
- **The Customer:** Secures the data, access configurations, and applications placed *in* the cloud. Negligence on the customer's part (e.g., misconfigured buckets) cannot be blamed on AWS.

> 
> 

### 1.2 Supply Chain Compromise & Unpatched Software

Developers rely heavily on third-party libraries (packaged code like payment processors or logging frameworks) to build applications faster. These are called **Software Dependencies**.

- **The Risk:** If a third-party library contains a vulnerability and is not patched, the attacker can exploit the main application.
- **Real-World Example (Log4j):** In 2021, a massive vulnerability was discovered in the Apache Log4j Java library. It allowed attackers to execute **Remote Code Execution (RCE)**, giving them the ability to remotely install malware, establish backdoors, and spread laterally across corporate networks. Continuous patching and vulnerability scanning are critical to preventing this.

---

## 📌 Part 2: Analyzing Cloud Security Risks (SentinelOne)

The session dove deep into emerging cloud risks and how attackers exploit them.

### 2.1 Insecure Cloud Configurations

Misconfigurations are the leading cause of cloud breaches.

- **Examples:** Publicly exposed S3 buckets containing sensitive PII (Personally Identifiable Information), open database ports, or overly permissive IAM policies (e.g., granting `AdministratorAccess` to a basic service).
- **Impact:** Regulatory fines (NDPR, GDPR, HIPAA) and massive reputational damage.
- **Mitigation:** Implement strict Data Classification, enforce the Principle of Least Privilege, and conduct regular Cloud Audits.

### 2.2 Insecure APIs (Application Programming Interfaces)

APIs are the middle-men that connect different software systems.

- **The Restaurant Analogy:** You (The User) want a bowl of soup. You give your order to the Waiter (The API). The Waiter takes the request to the Chef (The Server). The Chef cooks the soup, gives it to the Waiter, and the Waiter serves it back to you.
- **The Vulnerability:** If the API has weak authentication or lacks proper input validation, it can be exploited. For example, if User A logs in and uses a backend API script to request User B's resources, an insecure API might simply serve User B's data without validating if User A is authorized to view it (Broken Object Level Authorization).

> 
> 

### 2.3 Account Hijacking & Insider Threats

- **Account Hijacking:** Attackers steal credentials via phishing or brute force. Once inside, they establish **Persistent Access** by creating multiple hidden admin or service accounts. Even if the original compromised password is changed, the attacker remains deeply embedded in the system for years, gathering intelligence.
- **Insider Threats & Negligence:** A disgruntled employee leaking data, or an employee negligently picking up an infected, lost USB drive and plugging it into the corporate network.
- **Mitigation:** Enforce Multi-Factor Authentication (MFA), utilize Endpoint Detection and Response (EDR) software on all corporate devices, and disable unused USB ports via Active Directory.

### 2.4 Lack of Cloud Visibility (Shadow IT)

- **Shadow IT:** Employees spinning up cloud resources (servers, databases) without the knowledge or approval of the IT/Security department.
- **The Risk:** If the security team does not know a server exists, they cannot patch or monitor it, leaving a blind spot for attackers.
- **Mitigation:** Enforce strict resource tagging, centralized IAM creation processes, and continuous environment auditing.

---

## 📌 Part 3: Deep Dive into Denial of Service (DoS) Attacks

A DoS attack aims to consume a server's resources so heavily that legitimate users are blocked from accessing the application.

### 3.1 TCP SYN Flood Attack

TCP (Transmission Control Protocol) establishes connections using a **Three-Way Handshake**:

1. **SYN (Synchronize):** Client asks the server to connect.
2. **SYN-ACK:** Server acknowledges and leaves a port open, waiting for the final confirmation.
3. **ACK:** Client confirms, and communication begins.
- **The Exploit:** An attacker sends thousands of `SYN` requests but **never sends the final `ACK`**. The server allocates CPU and memory to keep thousands of connections "hanging" open. Eventually, the server runs out of resources, and legitimate customers' requests are dropped.

> 
> 

### 3.2 UDP Flood & DNS Amplification (Spoofing)

UDP (User Datagram Protocol) is a connectionless, fast protocol used for streaming and gaming. Attackers leverage DNS (Domain Name System) lookups to weaponize UDP.

- **The Mechanics:** An attacker wants to take down Victim A's server.
- **Spoofing:** The attacker sends a DNS query to a public DNS server, but mathematically alters the "Return Address" (Spoofing) to look like the request came from Victim A.
- **Amplification:** The attacker requests "ALL" DNS records (A, CNAME, MX, TXT, DNSKEY). The outbound request is tiny (e.g., 60 bits). However, the DNS server's response is massive (e.g., 6,000 bits).
- **The Impact:** The DNS server sends this massive 6,000-bit response to Victim A. The attacker automates this thousands of times, drowning Victim A's server in unwanted, heavy traffic.

> 
> 

---

## 📌 Part 4: Threat Intelligence & Honeypots

Security vendors (like Radware) do not just react to attacks; they proactively study attackers using **Deception Technology**.

- **Honeypots:** Security engineers deploy fake, highly vulnerable servers that look exactly like lucrative targets (e.g., a fake financial database).
- **The Trap:** When hackers break into the honeypot, specialized logging tools record their every move—packet sizes, protocols used, command-and-control IPs, and malware signatures. This raw data is analyzed in a lab to generate Global Threat Intelligence, allowing vendors to patch vulnerabilities before attackers use those same techniques on real companies.

---

## 📌 Part 5: Compliance, Auditing, and Change Management

### 5.1 The Importance of Evidence-Based Auditing (ISO 27001)

Operating in regulated industries requires strict adherence to compliance standards. When internal or external auditors review an environment, verbal confirmation is not enough; **Evidence** is required.

- If a policy dictates that a developer needs the CTO's approval to access an S3 bucket, the auditor will demand to see the email thread, the Jira ticket, and the CloudTrail log proving the workflow was followed.
- **Failure (Non-Conformance):** If evidence is missing, the company risks losing certifications (like ISO 27001), resulting in investors pulling funding and massive regulatory fines.

### 5.2 Change Advisory Board (CAB)

In enterprise environments, making infrastructural changes (like updating an IAM policy or modifying firewall rules) during business hours is prohibited to prevent accidental downtime.

- **The Process:** The engineer drafts a proposal detailing the implementation plan, contingency plan, and security impact.
- **The CAB Meeting:** A committee comprising Infrastructure, Security, and Legal teams reviews the proposal. Only if the "Ayes" have it is the change approved for a scheduled maintenance window.

---

## 📌 Part 6: Q&A Insights

- **Malware Development & Evasion:** Malware authors develop code and test it against platforms like VirusTotal to ensure it bypasses modern antivirus signatures before deploying it via phishing or Drive-by Downloads (malicious website ads).
- **OSINT (Open-Source Intelligence):** Attackers use public platforms like LinkedIn to profile target employees. They build trust over time before launching highly targeted spear-phishing campaigns.
- **Detecting APT Infiltration:** If an APT has breached an environment, indicators include unexplained high CPU/RAM usage (due to background surveillance processes), unusual outbound network traffic, and the sudden creation of unfamiliar user accounts.

[Cloud Security Operations & Cryptography Fundamentals](Cloud%20Security%20Operations%20&%20Threat%20Landscape/Cloud%20Security%20Operations%20&%20Cryptography%20Fundament%20323d65318cf6805fbf1fc6f2238c2ce4.md)