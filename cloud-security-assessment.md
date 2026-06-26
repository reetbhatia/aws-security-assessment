# Cloud Security Assessment Report

## Executive Summary

This assessment evaluates the security of an AWS-hosted deployment of OWASP Juice Shop, focusing on cloud infrastructure, application security, authentication, and data protection. Automated vulnerability analysis and manual validation were performed to identify exploitable weaknesses and verify their impact.

The assessment identified issues including outdated software dependencies, SQL injection, sensitive data exposure, weak password hashing, publicly exposed services, and missing cloud security controls. Each finding is supported by validation evidence and mapped to relevant OWASP Top 10 (2021) and CIS AWS Foundations guidance where applicable.

---

## Assessment Scope

**Cloud Platform**

* Amazon EC2
* AWS Security Groups
* Amazon EBS

**Application**

* OWASP Juice Shop
* SQLite Database
* Node.js Dependencies

**Assessment Activities**

* Vulnerability Analysis
* Manual Exploitation
* Network Enumeration
* Infrastructure Configuration Review

---

## Assessment Methodology

The assessment combined automated security analysis with manual validation to confirm identified vulnerabilities and eliminate false positives.

**Validation Techniques**

* AWS Inspector
* SQL Injection Testing
* SQLite Database Analysis
* Password Hash Analysis
* Nmap Reconnaissance
* Security Group Review
* Storage Encryption Verification

---

## Assessment Standards

* **OWASP Top 10 (2021)** – Application Security
* **CIS AWS Foundations Benchmark** – Cloud Security Configuration
* **AWS Security Best Practices** – Infrastructure Hardening

---

# Findings

---

## Initial Attack Surface

### 1. Vulnerable Application Dependencies

**Overview**

AWS Inspector identified **108 active package vulnerabilities**, including multiple Critical and High severity CVEs affecting outdated Node.js dependencies such as `elliptic`, `express-jwt`, `crypto-js`, `minimist`, and `vm2`. These publicly disclosed vulnerabilities remained unpatched and increased the application's exposure to known attacks.

**Impact**

Outdated third-party libraries are frequently targeted because exploit techniques are publicly available. Depending on the affected package, successful exploitation could lead to authentication bypass, denial-of-service, prototype pollution, or remote code execution.

**Evidence**

**Figure 1.** AWS Inspector identifying vulnerable application dependencies.

```markdown
![AWS Inspector Findings](../images/01-aws-inspector-findings.png)
```

**Recommendation**

* Update affected packages to supported versions.
* Remove deprecated or unused dependencies.
* Integrate automated dependency scanning into the CI/CD pipeline using AWS Inspector and `npm audit`.

**Framework Alignment**

| Framework           | Mapping                                  |
| ------------------- | ---------------------------------------- |
| **OWASP Top 10**  | A06 - Vulnerable and Outdated Components |
| **CIS AWS Foundations Benchmark**    | Continuous Vulnerability Management      |

---

### 2. Publicly Exposed Infrastructure

**Overview**

External reconnaissance identified multiple publicly accessible services, including **SSH (22)** and the application service running on **port 3000**. Review of the associated AWS Security Group confirmed unrestricted inbound access from `0.0.0.0/0`, significantly increasing the exposed attack surface.

**Impact**

Publicly accessible management and application services increase the likelihood of reconnaissance, brute-force attacks, and exploitation of application vulnerabilities. Broad Security Group rules also expand the attack surface by allowing unrestricted inbound connectivity from the Internet.

**Evidence**

**Figure 2.** Nmap scan identifying publicly accessible services.

```markdown
![Nmap Scan](../images/07-nmap-scan.png)
```

**Figure 3.** AWS Security Group permitting unrestricted inbound access.

```markdown
![Security Group Rules](../images/08-security-group-rules.png)
```

**Recommendation**

* Restrict Security Group rules to trusted IP ranges.
* Remove unnecessary public access.
* Use AWS Systems Manager Session Manager instead of exposing SSH where possible.
* Apply the principle of least privilege to all inbound rules.

**Framework Alignment**

| Framework                     | Mapping                                          |
| ----------------------------- | ------------------------------------------------ |
| **OWASP Top 10**           | Not Directly Applicable (Infrastructure Finding) |
| **CIS AWS Foundations Benchmark** | Network Security & Security Group Configuration  |

---

## Exploitation

### 3. SQL Injection Vulnerability

**Overview**

Manual testing confirmed that the application was vulnerable to **SQL injection**, allowing authentication controls to be bypassed and administrative access to be obtained without valid credentials. Successful exploitation demonstrated that user-supplied input was not adequately validated before being processed by the backend database.

**Impact**

Authentication bypass significantly reduces the effort required for an attacker to gain unauthorized access. Once administrative privileges are obtained, an attacker can access restricted functionality, retrieve sensitive information, and use the compromised application as a starting point for further attacks.

**Evidence**

**Figure 4.** Authentication bypass using SQL injection.

```markdown
![SQL Injection Login](../images/02-sqli-login.png)
```

**Figure 5.** Administrative dashboard accessed following successful exploitation.

```markdown
![Admin Dashboard](../images/03-admin-dashboard.png)
```

**Recommendation**

* Use parameterized queries or prepared statements for all database interactions.
* Implement server-side input validation and output encoding.
* Regularly test the application against the OWASP Top 10 using secure development practices.

**Framework Alignment**

| Framework                         | Mapping                                       |
| --------------------------------- | --------------------------------------------- |
| **OWASP Top 10**                 | A03 - Injection                               |
| **CIS AWS Foundations Benchmark** | Not Directly Applicable (Application Finding) |

### 4. Unauthorized Access to Sensitive Data

**Overview**

Following successful application access, the assessment confirmed that sensitive information stored within the application's SQLite database could be directly queried. User account records, email addresses, password hashes, and payment card information were accessible without additional security controls, demonstrating the impact of the compromised application.

**Impact**

Unauthorized access to sensitive application data increases the risk of account compromise, identity theft, financial fraud, and credential reuse attacks. Exposure of customer information also creates compliance and privacy concerns if the affected application processes real user data.

**Evidence**

**Figure 6.** User account records retrieved from the SQLite database.

```markdown
![User Records](../images/04-user-records.png)
```

**Figure 7.** Payment card information retrieved during database inspection.

```markdown
![Payment Data](../images/05-payment-data.png)
```

**Recommendation**

* Restrict direct access to application databases.
* Encrypt sensitive data where appropriate and minimize unnecessary data retention.
* Apply role-based access controls to ensure users and services can only access the data required for their function.

**Framework Alignment**

| Framework                         | Mapping                                                   |
| --------------------------------- | --------------------------------------------------------- |
| **OWASP Top 10**                | A01 - Broken Access Control, A02 – Cryptographic Failures |
| **CIS AWS Foundations Benchmark** | Data Protection & Access Control                          |

### 5. Weak Password Hashing

**Overview**

Inspection of the application's user database revealed that passwords were stored using the **MD5 hashing algorithm**. Validation confirmed that multiple password hashes could be successfully recovered using publicly available hash-cracking resources, demonstrating that the stored credentials provided limited resistance against offline attacks.

**Impact**

Weak password hashing significantly reduces the effort required to recover user credentials once password hashes are exposed. Recovered passwords may enable account compromise, credential reuse attacks against other services, and unauthorized access to additional systems where users have reused the same password.

**Evidence**

**Figure 8.** MD5 password hashes successfully recovered during validation.

```markdown
![MD5 Password Crack](../images/06-md5-crack.png)
```

**Recommendation**

* Replace MD5 with a modern password hashing algorithm such as **bcrypt**, **Argon2**, or **PBKDF2**.
* Enforce strong password policies and enable multi-factor authentication for privileged accounts.
* Monitor for compromised credentials and require password resets where weak hashes have been exposed.

**Framework Alignment**

| Framework                         | Mapping                                          |
| --------------------------------- | ------------------------------------------------ |
| **OWASP Top 10**                 | A07 - Identification and Authentication Failures |
| **CIS AWS Foundations Benchmark** | Identity & Authentication Best Practices         |

---

## Defensive Controls & Hardening

### 6. Unencrypted Storage

**Overview**

Review of the EC2 storage configuration confirmed that the attached Amazon EBS volume was **not encrypted**. While the application remained operational, the absence of encryption at rest increased the risk of unauthorized access to sensitive information if the underlying storage volume were compromised or improperly accessed.

**Impact**

Unencrypted storage weakens the protection of sensitive application data by leaving it dependent on access controls alone. In cloud environments, encryption at rest provides an additional layer of defense that helps protect data in the event of unauthorized access, snapshot exposure, or storage misconfiguration.

**Evidence**

**Figure 9.** Amazon EBS volume configured without encryption.

```markdown
![EBS Encryption](../images/09-ebs-encryption.png)
```

**Recommendation**

* Enable Amazon EBS encryption using AWS KMS-managed keys.
* Encrypt snapshots containing sensitive application data.
* Apply encryption-by-default for future storage volumes.

**Framework Alignment**

| Framework                         | Mapping                         |
| --------------------------------- | ------------------------------- |
| **OWASP Top 10**                 | A02 - Cryptographic Failures    |
| **CIS AWS Foundations Benchmark** | Storage Protection & Encryption |

## Security Hardening Opportunities

The assessment identified several opportunities to further strengthen the security posture of the environment beyond the validated findings presented in this report. While these items were not documented as standalone vulnerabilities, implementing them would improve visibility, reduce attack surface, and support long-term security operations.

### Identity & Access Management

* Apply the principle of least privilege to IAM users and roles.
* Enable Multi-Factor Authentication (MFA) for privileged accounts.
* Replace long-lived administrative credentials with temporary IAM roles where possible.

### Monitoring & Detection

* Enable AWS CloudTrail to capture management activity across the environment.
* Configure Amazon GuardDuty to continuously monitor for suspicious behavior.
* Enable VPC Flow Logs and centralize security logs using Amazon CloudWatch for improved visibility and incident investigation.

### Infrastructure Security

* Restrict Security Group rules to trusted administrative networks.
* Enable encryption by default for all new Amazon EBS volumes and snapshots.
* Perform regular vulnerability scanning and dependency updates using AWS Inspector.

### Secure Development Practices

* Validate user input using parameterized queries and server-side validation.
* Integrate automated dependency scanning into the CI/CD pipeline.
* Perform periodic penetration testing and security reviews to identify newly introduced vulnerabilities.

---

## Conclusion

The assessment identified practical security improvements across both the application and its supporting AWS infrastructure. Implementing these recommendations would reduce the attack surface, strengthen cloud security controls, and improve the overall resilience of the environment.

