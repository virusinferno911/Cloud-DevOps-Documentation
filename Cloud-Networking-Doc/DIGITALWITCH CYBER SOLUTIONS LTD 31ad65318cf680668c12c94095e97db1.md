# DIGITALWITCH CYBER SOLUTIONS LTD

Cloud Security Incident Report

**PART 1: RISK AND THREAT ANALYSIS**

AWS Cloud Infrastructure Security Assessment

**Prepared by: Oluwasheyi Olayemi Ojelade**, Cloud Security Analyst (Newly Assigned)

**Client Organisation: DigitalWitch Cyber Solutions Ltd**

**Date:** March 2026

**Classification:** CONFIDENTIAL

**Threat Actors Identified:** APT32 (OceanLotus) and APT41 (Double Dragon)

# **Background and Context**

DigitalWitch Cyber Solutions Ltd recently completed the migration of approximately 80% of its IT infrastructure to Amazon Web Services (AWS). Within weeks of the migration going live, multiple departments began reporting access anomalies, and a ransomware note was discovered on several cloud-hosted virtual machines demanding payment in Monero, a privacy-focused cryptocurrency commonly favoured by sophisticated threat actors due to its resistance to blockchain tracing.

As the newly assigned Cloud Security Analyst, I was tasked with conducting a full risk and threat analysis to understand what happened, how severe the exposure is, and what risks remain after remediation efforts are put in place. This document covers Part 1 of that investigation.

The indicators observed across the environment are consistent with the tactics, techniques, and procedures (TTPs) associated with two well-documented Advanced Persistent Threat (APT) groups.

- APT32 (OceanLotus), a Vietnam-linked group known for targeting enterprises, cloud environments, and media organisations through spear-phishing, malware deployment, and lateral movement across cloud services.
- APT41 (Double Dragon), a China-linked group that operates both state-sponsored espionage campaigns and financially motivated operations including ransomware deployment. They have a documented history of exploiting cloud misconfigurations and overpermissioned IAM identities.

## **Section 1: Risk Identification and Classification**

The following risks were identified from the incident evidence gathered across the AWS environment. Each risk is classified using the Total Risk formula:

**Total Risk = Threat x Vulnerability x Asset Value**

Each factor is scored on a scale of 1 (Very Low) to 5 (Critical). Maximum possible score: 125.

---

| **Score** | **Threat Level** | **Vulnerability** | **Asset Value** | **Risk Band** |
| --- | --- | --- | --- | --- |
| 1 | Very Low | Very Low | Minimal | 1-20: Low |
| 2 | Low | Low | Minor | 21-49: Medium |
| 3 | Medium | Medium | Moderate | 50-79: High |
| 4 | High | High | Significant | 80-99: Very High |
| 5 | Critical | Critical | Business Critical | 100-125: Critical |

**Risk 1: Overpermissioned IAM Roles Exploited for Privilege Escalation**

**Description:**

During the investigation, it was discovered that several AWS Identity and Access Management (IAM) roles were configured with far broader permissions than their intended function required. For example, an IAM role assigned to a development EC2 instance had AdministratorAccess attached, a policy that grants unrestricted access to every AWS service and resource in the account. Another role used by an application service had s3:* permissions scoped to all buckets rather than the specific bucket it actually needed.

This is a classic violation of the Principle of Least Privilege (PoLP), which states that any identity, whether a human user, a service, or an application, should only be granted the minimum access it needs to perform its specific function, and nothing more.

In this incident, APT41 is known to conduct IAM enumeration after gaining an initial foothold. Once inside, they query AWS with commands such as 'iam:ListRoles', 'iam:GetPolicy', and 'sts:AssumeRole' to map out what permissions are available. Finding an overpermissioned role, they assumed it using stolen credentials or an exploited metadata service endpoint (IMDS), then used the escalated access to move laterally across cloud services, access S3 buckets, and ultimately deploy ransomware payloads to EC2 instances.

| **Risk Factor** | **Score (1-5)** | **Justification** |
| --- | --- | --- |
| **Threat (APT41 IAM Exploitation)** | **5** | APT41 has a confirmed history of IAM abuse in cloud breaches. Tools like Pacu (AWS exploitation framework) automate IAM enumeration. |
| **Vulnerability (Overpermissioned Roles)** | **5** | AdministratorAccess on application roles is a severe misconfiguration. No MFA enforcement on role assumption was observed. |
| **Asset Value (IAM and Core Infrastructure)** | **5** | Compromised IAM access gives an attacker control over the entire AWS account. This is the highest-value asset in a cloud environment. |
| **TOTAL RISK SCORE** | **125 / 125** | **CRITICAL. Full account compromise is possible from this single vulnerability.** |

**Real-World Parallel:** In 2020, APT41 was indicted by the US Department of Justice after compromising over 100 organisations. In several cases, overpermissioned cloud roles were directly exploited to harvest data and deploy malware. The tools they used, including Speculoos and HIGHNOON malware, relied on lateral movement enabled by excessive IAM permissions.

**Risk 2: Unauthorised Cloud Storage Access and Data Exfiltration via S3**

**Description:**

Multiple departments reported denial of access to internal resources. At the same time, threat intelligence and CloudTrail logs revealed unusual GetObject, PutObject, and ListBuckets API calls originating from IP addresses not associated with any DigitalWitch employee. Some S3 buckets had public-read ACLs that were never intentionally set, likely a misconfiguration carried over from a development environment during the migration.

In a real scenario like this, the attacker likely performed a combination of two techniques. First, S3 bucket enumeration, scanning for publicly accessible buckets containing sensitive data such as database backups, configuration files with embedded credentials, and employee records. Second, using the IAM role access gained in Risk 1, they accessed private buckets that were otherwise locked down but accessible from within the compromised AWS account context.

The access denial reports from staff likely occurred because the attacker, after assuming a compromised role, modified bucket policies or ACLs to restrict legitimate access while preserving their own. This is a known data hostage tactic used alongside ransomware campaigns.

| **Risk Factor** | **Score (1-5)** | **Justification** |
| --- | --- | --- |
| **Threat (Data Exfiltration via S3)** | **4** | APT32 and APT41 both conduct cloud storage enumeration as part of their reconnaissance phase. Tools like GrayhatWarfare and custom scripts automate this. |
| **Vulnerability (Misconfigured ACLs and Bucket Policies)** | **4** | Public ACLs on sensitive buckets and absence of S3 Block Public Access settings at the account level represent a significant exposure. Migration from on-prem often introduces these misconfigurations. |
| **Asset Value (Sensitive Business Data)** | **5** | S3 buckets at DigitalWitch likely store client data, application backups, financial records, and configuration files. Loss or exposure of this data carries regulatory, legal, and reputational consequences. |
| **TOTAL RISK SCORE** | **80 / 125** | **VERY HIGH. Data breach risk is active. Exfiltration may have already occurred before detection.** |

**Real-World Parallel:** The 2019 Capital One breach, while not attributed to an APT, demonstrates the exact vulnerability chain. A misconfigured WAF allowed an attacker to perform SSRF requests to the EC2 metadata service (IMDS), retrieve temporary IAM credentials, and then list and download over 100 million records from S3 buckets. The same IMDS exploitation technique is documented in APT41 playbooks.

**Risk 3: Ransomware Deployment on EC2 Instances via Compromised Firewall Rules**

**Description:**

A ransomware note demanding payment in Monero was found on several EC2 virtual machines. Alongside this, the security team discovered that a set of previously whitelisted IP addresses had been added to AWS Security Group inbound rules, effectively opening direct SSH and RDP access to production instances from attacker-controlled infrastructure.

This is a two-stage attack. In the first stage, the attacker abused the compromised IAM credentials (from Risk 1) to call the ec2:AuthorizeSecurityGroupIngress API, silently adding their own IPs as allowed sources. AWS Security Groups, unlike on-premises firewalls, can be modified programmatically by anyone with the right IAM permission. With no alerting on Security Group changes, this modification went undetected.

In the second stage, with SSH access now open to their IPs, the attacker connected directly to production EC2 instances, dropped a ransomware payload (likely a custom Linux encryptor targeting cloud-based workloads), and executed it. The use of Monero as the demanded currency is consistent with APT41 financial operations and is chosen specifically because Monero transactions are unlinkable and untraceable, unlike Bitcoin.

The unusual outbound traffic to unknown IP addresses reported earlier is consistent with the ransomware performing command-and-control (C2) beaconing and potentially exfiltrating data before encryption as a double extortion tactic. APT41 is known to use double extortion, meaning they steal data first and then encrypt it, so they can threaten to publish the data even if the victim has backups.

| **Risk Factor** | **Score (1-5)** | **Justification** |
| --- | --- | --- |
| **Threat (Ransomware Deployment by APT41)** | **5** | Ransomware is confirmed and active on the environment. APT41 has documented ransomware capabilities. The Monero demand and double-extortion indicators confirm a sophisticated actor. |
| **Vulnerability (Unmonitored Security Group Changes)** | **4** | No CloudTrail alerting on ec2:AuthorizeSecurityGroupIngress allowed the attacker to open SSH/RDP access without triggering any alarm. Absence of GuardDuty or Config Rules for Security Group monitoring. |
| **Asset Value (EC2 Production Workloads)** | **5** | EC2 instances run core business applications. Encryption of these workloads causes immediate operational downtime, revenue loss, and potential SLA breaches with clients. |
| **TOTAL RISK SCORE** | **100 / 125** | **CRITICAL. Active ransomware with C2 beaconing. Double extortion likely. Immediate containment required.** |

**Real-World Parallel:** In 2021, APT41 operators were linked to campaigns targeting managed service providers (MSPs) where EC2-equivalent cloud VMs were compromised via stolen credentials, firewall rules were silently modified, and ransomware was deployed as a secondary payload after data exfiltration was complete. The Winnti malware family, associated with APT41, has Linux variants designed for cloud server environments.

**Risk Summary Table**

| **Risk** | **Threat (T)** | **Vulnerability (V)** | **Asset Value (A)** | **Total Risk (T x V x A)** |
| --- | --- | --- | --- | --- |
| **IAM Privilege Escalation** | 5 | 5 | 5 | **125 – CRITICAL** |
| **S3 Unauthorised Access / Exfiltration** | 4 | 4 | 5 | **80 – VERY HIGH** |
| **Ransomware via Modified Firewall Rules** | 5 | 4 | 5 | **100 – CRITICAL** |

**Figure 1: Reconstructed APT Attack Chain (DigitalWitch AWS Environment)**

The diagram below reconstructs how the threat actor likely moved through the AWS environment based on the observed indicators.

| **1** | **Initial Access. Spear-phishing or credential stuffing.**
APT32 / APT41 obtain AWS access keys or IAM user credentials via phishing email targeting DigitalWitch staff, or by finding keys accidentally committed to a public GitHub repository during the migration process. |
| --- | --- |

| **2** | **Reconnaissance. IAM Enumeration.**
Attacker runs 'iam:ListRoles', 'iam:GetPolicy', 'sts:GetCallerIdentity', and 'ec2:DescribeInstances' to map the environment and identify overpermissioned IAM roles available for assumption. |
| --- | --- |

| **3** | **Privilege Escalation. Role Assumption.**
Using 'sts:AssumeRole', the attacker assumes an IAM role with AdministratorAccess. They now have unrestricted access to every AWS service in the account. |
| --- | --- |

| **4** | **Lateral Movement and S3 Exfiltration.**
Attacker lists and downloads data from S3 buckets using 's3:ListBuckets' and 's3:GetObject'. Modifies bucket ACLs to deny legitimate users. Stolen data is staged for double extortion. |
| --- | --- |

| **5** | **Persistence. Security Group Modification.**
Attacker calls 'ec2:AuthorizeSecurityGroupIngress' to add their own IPs to production Security Groups, opening SSH port 22 and RDP port 3389. No alert fires because CloudTrail monitoring was not configured for this action. |
| --- | --- |

| **6** | **Impact. Ransomware Deployment and C2 Beaconing.**
Attacker SSH's directly into EC2 instances, drops a Linux ransomware payload, and executes it. Ransomware encrypts workloads and drops a note demanding Monero. C2 beaconing starts to unknown external IPs. Double extortion threat: pay or stolen data gets published. |
| --- | --- |

**Figure 2: Risk Heat Map**

The heat map below plots the three identified risks by Likelihood (Threat) on the vertical axis and Impact (Asset Value x Vulnerability) on the horizontal axis.

|  | **Very Low** | **Low** | **Medium** | **High / Critical** |
| --- | --- | --- | --- | --- |
| **Critical (5)** |  |  | **RISK 3 Ransomware** | **RISK 1 IAM Escalation** |
| **High (4)** |  |  | **RISK 2 S3 Exfiltration** |  |
| **Medium (3)** |  |  |  |  |
| **Low (1-2)** |  |  |  |  |

*Vertical axis: Likelihood / Threat Score.   Horizontal axis: Combined Impact (Vulnerability x Asset Value).*

## **Section 2: Residual Risk After Remediation**

Residual Risk is the level of risk that remains in the environment after controls, patches, and remediation measures have been applied. It acknowledges that no set of controls completely eliminates risk. Some exposure will always remain, either because threats evolve, controls are imperfect, or new vulnerabilities emerge.

Residual Risk is calculated as:

**Residual Risk = Total Risk – (Effectiveness of Controls Applied)**

---

The following remediation actions are assumed to have been applied: IAM roles stripped to least-privilege permissions, MFA enforced on all IAM users and role assumptions, S3 Block Public Access enabled at the account level, Security Group rules audited and tightened, CloudTrail alerts configured for sensitive API calls, AWS GuardDuty enabled, and infected EC2 instances isolated and reimaged.

| **Risk** | **Total Risk (Before)** | **Residual Risk (After)** | **Controls Applied** |
| --- | --- | --- | --- |
| **IAM Privilege Escalation** | **125 – CRITICAL** | **16 – LOW** | IAM roles scoped to specific resources and actions. MFA enforced on AssumeRole. SCP (Service Control Policies) applied at the organisation level. Access Analyzer enabled. Residual risk remains due to the possibility of zero-day credential theft. |
| **S3 Unauthorised Access** | **80 – VERY HIGH** | **20 – LOW** | Block Public Access enabled. Bucket policies reviewed and tightened. S3 Object-level logging turned on. Macie enabled for automated sensitive data detection. Residual risk remains because insider threats or future misconfiguration are never fully eliminated. |
| **Ransomware via Firewall Modification** | **100 – CRITICAL** | **25 – LOW** | Security Group changes now trigger CloudWatch alerts. EC2 instances reimaged from clean AMIs. GuardDuty enabled for threat detection. Incident response runbook created. Residual risk remains because a determined APT can adapt TTPs and find new entry points. |

**Important Note on Residual Risk**

Residual risk does not mean the organisation is safe. It means the risk has been reduced to an acceptable level based on the controls applied. DigitalWitch must continue to review this residual risk on a regular schedule, especially because APT32 and APT41 are persistent actors who update their toolkits and techniques. A control that is effective today may not be effective against a next-generation variant of their malware or a newly discovered AWS API exploit.

The organisation should also accept that some data may already have been exfiltrated before detection. Residual risk in that case extends to regulatory exposure under data protection laws, which cannot be eliminated by technical controls alone.

## **Section 3: Secondary Risks Arising from Remediation**

When aggressive remediation is applied quickly during an active incident, it often introduces a new category of risk. These are secondary risks, unintended negative consequences that arise as a direct result of the controls and changes put in place to address the primary threats. Ignoring secondary risks is one of the most common mistakes made during cloud incident response.

**Secondary Risk 1: Operational Disruption from Over-Restrictive IAM Changes**

Stripping IAM roles to least-privilege in an emergency often means removing permissions that legitimate applications, CI/CD pipelines, and automated services depend on. For example:

- A deployment pipeline using GitHub Actions with an IAM role that previously had s3:PutObject on all buckets may break immediately if that permission is scoped to a specific bucket that does not match its deployment target.
- Lambda functions that previously read configuration from SSM Parameter Store may fail if the ssm:GetParameter permission is removed during the cleanup sweep.
- Monitoring agents such as CloudWatch agent or Datadog running on EC2 may lose their ability to push metrics if their attached IAM policies are tightened without checking their specific requirements first.

The secondary risk here is business disruption and outage of internal tools, potentially impacting the ability to detect future threats while the monitoring stack is broken.

**Mitigation:** Before applying IAM changes in bulk, run AWS IAM Access Analyzer and review CloudTrail logs to understand which permissions each role has actually used in the last 90 days. AWS provides a 'Last Accessed' feature on IAM that shows exactly which services a role has used. Only remove permissions that have genuinely never been used.

**Secondary Risk 2: Loss of Legitimate External Access After Firewall Tightening**

Removing whitelisted IPs and tightening Security Group rules can accidentally block legitimate business tools and third-party integrations that the company depends on. For example:

- Remote working staff connecting to internal cloud resources via a static IP that was grouped with the attacker-added IPs during the audit may find themselves locked out.
- Third-party monitoring services such as PagerDuty, Pingdom, or a managed SOC provider that has IP ranges whitelisted may lose visibility into the environment.
- Business partners, API consumers, or client systems that integrate with DigitalWitch services over specific IPs may experience service interruptions, potentially triggering contractual SLA penalties.

**Mitigation:** Before removing IP entries from Security Groups, cross-reference each IP against a legitimate access register. Use AWS VPC Flow Logs to confirm whether each whitelisted IP has recently generated expected traffic patterns or suspicious ones. Implement AWS Systems Manager Session Manager as a replacement for direct SSH, eliminating the need for open inbound ports entirely.

**Secondary Risk 3: Loss of Forensic Evidence During Containment**

When EC2 instances affected by ransomware are immediately shut down or terminated as part of containment, volatile memory data that could be critical to the forensic investigation is permanently lost. This includes:

- In-memory encryption keys that could potentially allow decryption of the ransomware payload.
- Running process lists, network connections, and open file handles that would help reconstruct exactly how the attacker moved within each instance.
- Evidence of the specific ransomware variant, which is needed to determine whether a public decryption tool exists.

Additionally, if S3 versioning was not enabled before the attacker modified bucket ACLs or deleted objects, those changes may be unrecoverable, and the forensic audit trail of exactly what was modified becomes incomplete.

**Mitigation:** Follow the order: Isolate first, image second, terminate last. Before shutting down any compromised EC2 instance, take a full EBS snapshot and, where possible, capture a memory dump using a tool like LiME (Linux Memory Extractor) or AWS Systems Manager Run Command. Enable S3 Versioning and Object Lock on all buckets going forward so that even if an attacker modifies or deletes objects, the previous versions are preserved.

**Secondary Risk 4: Staff Lockout and Productivity Loss from Forced MFA Rollout**

Enforcing MFA across all IAM users immediately after an incident is the right thing to do, but if done without preparation it creates its own disruption. Staff who have never used MFA before may be locked out of their accounts, particularly:

- Developers with programmatic access keys that do not support MFA-based flows without code changes.
- Automated service accounts that were created as IAM users (rather than roles) and cannot complete an MFA challenge.
- Contractors or external consultants with IAM access who are not reachable quickly during an emergency rollout.

**Mitigation:** Separate human IAM users from service accounts. Human users should be migrated to AWS IAM Identity Centre (formerly SSO) with MFA enforced at that layer. Service accounts should be replaced with IAM roles entirely, as roles use temporary credentials and cannot be phished. Roll MFA out in waves with a grace period, starting with admin-level users first.

**Secondary Risk Summary Table**

| **Secondary Risk** | **Triggered By** | **Potential Impact** | **Recommended Mitigation** |
| --- | --- | --- | --- |
| **Over-restrictive IAM** | Emergency IAM cleanup | Broken pipelines, app outages, blind monitoring | Use IAM Last Accessed data before removing permissions |
| **Blocked Legitimate External Access** | Security Group tightening | Locked-out staff and clients, SLA breaches | Cross-reference IPs before removal, use SSM Session Manager |
| **Lost Forensic Evidence** | Instance termination during containment | Incomplete investigation, decryption impossible | Isolate, snapshot, then terminate. Enable S3 Versioning. |
| **Staff and Service Lockout from MFA Rollout** | Forced MFA enforcement | Productivity loss, access gaps for service accounts | Migrate to IAM Identity Centre. Replace service IAM users with roles. |

## **Conclusion:**

This Part 1 analysis confirms that DigitalWitch Cyber Solutions Ltd is facing a sophisticated, multi-vector cloud attack consistent with the known TTPs of APT32 and APT41. Three critical-to-very-high risks have been identified and scored using the Total Risk formula, with IAM privilege escalation and ransomware deployment both scoring at the maximum severity level.

Remediation through IAM tightening, S3 access controls, Security Group auditing, and activation of AWS native threat detection tools (GuardDuty, CloudTrail alerting, Macie) is expected to bring residual risk down to a low and manageable level. However, the organisation must remain aware that residual risk is never zero, and that APT-level threat actors will adapt.

Just as importantly, the secondary risks introduced by remediation, including broken pipelines, blocked partners, lost evidence, and staff lockouts, must be actively managed to avoid trading one set of problems for another. Effective incident response is not just about closing the door on the attacker. It is about doing so without accidentally locking out the people who need to be inside.

Parts 2 through 4 of this assessment will cover Incident Response Planning, Cloud Security Architecture Recommendations, and Threat Intelligence Mapping in detail.

*End of Part 1 Report.   DigitalWitch Cyber Solutions Ltd.   Confidential.*