# Introduction to Cyber Security in Cloud & DevOps

**Project Author:** Oluwasheyi Ojelade
**Status:** ✅ Completed / Documented
**Focus:** Security Principles, CIA Triad, Access Control Models, and Risk Management Mathematics.

---

## Part 1: The Evolution of Security & Security Domains

### **1.1 Traditional Data Centers vs. Cloud Security**

- **Traditional Data Centers:** Historically, organizations owned physical servers. They were responsible for 100% of the security (physical locks, network firewalls). The attack vectors were smaller but entirely the company's burden.
- **Cloud Environments (AWS, Azure, GCP):** The responsibility is now shared. You no longer manage the physical hardware, which introduces **new attack vectors**:
    - Cloud Misconfigurations (e.g., leaving an S3 bucket public).
    - Access Control Mismanagement (e.g., giving overly permissive IAM roles).

### **1.2 The Three Branches of Security**

Security is structured like a pyramid. It is crucial to know where an operation falls.

1. **Information Security (The Broadest):** Securing *any* type of information, whether digital or physical (paper, voice, knowledge).
    - *Examples:* "Clean Desk Policies" (not leaving sensitive papers on your desk), locking file cabinets, and Data Classification policies.
2. **IT Security (The Infrastructure):** Protecting the technology infrastructure and hardware that processes the data.
    - *Examples:* Securing physical servers, operating systems, databases, and endpoint devices.
3. **Cyber Security (The Internet Layer):** Protecting anything exposed to or connected to the internet.
    - *Examples:* Protecting web apps (like Zoom), defending against hackers, malware, ransomware, and phishing. It involves Threat Intelligence and Incident Response.

---

## Part 2: The CIA Triad & States of Data

Every security control in existence is built on the **CIA Triad**. If a system fails in any of these three areas, it is vulnerable.

### **1. Confidentiality**

Guaranteeing that data remains secure and **inaccessible to unauthorized users**. (e.g., Facebook verifying your username and password before showing your messages).

- **The 3 States of Data:**
    1. **Data at Rest:** Data sleeping comfortably in storage (e.g., an AWS S3 bucket, a hard drive). Easiest to encrypt.
    2. **Data in Motion:** Data moving across a network (e.g., sending an email or a WhatsApp message). Secured using HTTPS and SSL.
    3. **Data in Use:** Data actively being processed by the CPU/RAM. **(Most Vulnerable State)**.
        - *Analogy:* When you put your ATM card into a POS machine, the data is temporarily decrypted in the RAM so the transaction can process. If a hacker accesses the memory at that exact millisecond, they can steal the raw data.

### **2. Integrity**

Ensuring data is not altered, modified, or intercepted during transit or storage.

- If user A sends "Hello", the server must receive "Hello", not "Hack". HTTPS/SSL encryption ensures that even if hackers intercept the data mid-air, they cannot modify it.

### **3. Availability**

Guaranteeing that data and services are accessible to *authorized users* whenever they need it.

- **Threats to Availability:** DDoS attacks (flooding a server until it crashes) or poor capacity planning.
- *Analogy:* The instructor used the old JAMB/NYSC registration portals as an example. When 1,200 students tried to access a server built for 1,000 requests per second on deadline day, the server crashed (Denial of Service). Cloud Auto-scaling fixes this today.

---

## Part 3: IAAA (Identity & Access Management Framework)

You cannot secure a system without IAAA.

### **1. Identification**

Who are you? (e.g., Your Name, Username, Passport Number, Email). Without an identity, you cannot be tracked or audited.

### **2. Authentication (Proving who you are)**

There are three main types (factors) of authentication:

- **Type 1 (Something you know):** Passwords, PINs.
- **Type 2 (Something you have):** Hardware tokens, OTP sent to your phone.
- **Type 3 (Something you are):** Biometrics (Fingerprints, Facial Recognition, Iris scans).
    - *Real-World Application:* Financial apps like OPay use combined authentication. If you attempt a suspicious transaction to a new recipient, it requires a Password (Type 1) AND a Facial Scan (Type 3) to authenticate the transfer.

### **3. Authorization (What you are allowed to do)**

Just because you are authenticated doesn't mean you can touch everything.

- **Least Privilege:** Giving users the absolute minimum access required to do their job.
    - *Analogy:* A bank teller is authorized to enter the banking hall and restrooms, but they are NOT authorized to enter the Server Room or the Manager's Office.
- **Need to Know:** Even if you have clearance, you cannot view a file unless it is required for your current task.

### **4. Accountability (Non-Repudiation)**

Every action must be traced back to a specific identity so the user **cannot deny** doing it (Non-repudiation).

- *Example:* If a hacker steals money and transfers it to a friend, Cloud Observability tools (like **Datadog**) log the IP address, browser type, timestamp, and action. Law enforcement can use these logs to trace and prosecute the hacker.

---

## Part 4: Access Control Models

Organizations enforce authorization using different models:

1. **DAC (Discretionary Access Control):** The owner of the data decides who gets access. Common in standard Operating Systems (like sharing a folder in Windows).
2. **MAC (Mandatory Access Control):** Access is based on strict clearance levels (e.g., Top Secret, Confidential). Used heavily by the Military and Government Contractors.
3. **RBAC (Role-Based Access Control):** Access is granted based on your job department.
    - *Example:* The HR Role has access to Payroll and Employee Records. The IT Role has access to the Data Center. HR cannot access the Data Center.
4. **ABAC (Attribute-Based Access Control) & Context-Based:** Access is granted based on environmental conditions.
    - *Example:* If you try to log in from a restricted country (Iran/Iraq), or if you try to log in using an outdated Chrome browser (Version 5), or if you use an unmanaged personal laptop (BYOD), the system will dynamically block you via a "Conditional Access Policy."

---

## Part 5: The Mathematics of Risk Management

Risk isn't just a feeling; it is calculated using hard math.
**The Golden Formula:** `Risk = Threat × Vulnerability × Asset Value`

- **Threat:** A danger waiting to happen (e.g., Thieves, Hackers).
- **Vulnerability:** A weakness the threat can exploit (e.g., Weak doors, Misconfigured Firewalls).
- **Asset:** What is being stolen/damaged (e.g., $10,000 cash, a Database).

### **5.1 Quantitative Risk Analysis (The Dollar Value)**

This calculates exact financial losses to present to a CEO/CTO.

**Formulas & Terms:**

- **AV (Asset Value):** E.g., A laptop is worth $1,000, and the customer data on it is worth $10,000. Total AV = $11,000.
- **EF (Exposure Factor):** If the laptop is stolen, it is 100% gone.
- **SLE (Single Loss Expectancy):** `AV × EF`. ($11,000 × 100% = $11,000 per lost laptop).
- **ARO (Annual Rate of Occurrence):** How often it happens per year. (e.g., We lose 25 laptops a year).
- **ALE (Annualized Loss Expectancy):** `SLE × ARO`. ($11,000 × 25 = $275,000 lost per year).
- **ROI on Security:** If buying endpoint security costs $100,000 (Countermeasures), the company saves $175,000 per year.

*Real-World Analogy:* The Nigerian government was losing 500 Billion Naira a year to pipeline vandalism. They invested 48 Billion Naira in private security. Production increased, and they secured 1.2 Trillion Naira. The ROI justified the security expense.

### **5.2 Qualitative Risk Analysis**

Used when you can't put an exact dollar amount on something (like bad PR or reputation). It categorizes risk by **Severity** (Critical, High, Medium, Low) and **Likelihood** (Almost Certain, Unlikely).

---

## Part 6: Risk Responses & Metrics

Once a risk is identified, a company must choose one of four responses:

1. **Mitigate:** Apply a patch, fix the firewall, or change the locks.
2. **Transfer:** Buy Cyber Insurance to cover the financial loss if a hack happens.
3. **Accept (with Compensating Controls):** Accept the risk but monitor it heavily (e.g., if you can't install MFA on an old app, you accept the risk but enable extreme logging).
4. **Avoid:** Stop the activity entirely. (e.g., Stop issuing laptops to employees and only use immovable desktops).
- *Note:* **Rejecting/Ignoring** a risk is illegal/unacceptable and can lead to auditor penalties or jail time.

### **Security Metrics to Track:**

- **KGI (Key Goal Indicator):** The ultimate business objective (e.g., Ensure customer data meets compliance by year-end).
- **KPI (Key Performance Indicator):** Proves the system is working toward the goal (e.g., 100% of servers have logging enabled).
- **KRI (Key Risk Indicator):** Warns that the goal is in danger (e.g., Incident response time is taking 3 days instead of the mandated 24 hours).

[Cloud Security Operations & Threat Landscape](Introduction%20to%20Cyber%20Security%20in%20Cloud%20&%20DevOps/Cloud%20Security%20Operations%20&%20Threat%20Landscape%20323d65318cf68070a245cacec05f82b7.md)

[**DIGITALWITCH CYBER SOLUTIONS LTD**](Introduction%20to%20Cyber%20Security%20in%20Cloud%20&%20DevOps/DIGITALWITCH%20CYBER%20SOLUTIONS%20LTD%2031ad65318cf680668c12c94095e97db1.md)