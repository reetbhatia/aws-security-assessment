# ☁️ AWS Cloud Security Assessment

> How secure is an AWS environment when you stop assuming every configuration is correct?

---

A practical cloud security assessment of an AWS-hosted **OWASP Juice Shop** environment, demonstrating how cloud infrastructure and application vulnerabilities can be identified, validated, and remediated through a structured security assessment.

> **Detailed Assessment Report:** [Cloud Security Assessment Report](cloud-security-assessment.md)

---

## Project Overview

This project evaluates the security of an AWS-hosted web application by combining automated vulnerability analysis with manual validation. The assessment examines the application's attack surface, validates exploitable security weaknesses, and provides practical remediation recommendations aligned with industry security frameworks.

The objective was not only to identify vulnerabilities, but also to demonstrate a structured approach to assessing cloud-hosted workloads.

---

## Assessment Scope

The assessment included:

* Amazon EC2
* AWS Security Groups
* Amazon EBS
* OWASP Juice Shop
* SQLite Database
* Network Exposure
* Authentication & Credential Storage
* Application Dependencies

---

## Key Findings

The assessment validated security weaknesses across multiple layers of the environment, including:

* Vulnerable third-party application dependencies
* Publicly exposed network services
* SQL injection leading to administrative access
* Unauthorized access to sensitive application data
* Weak password hashing (MD5)
* Unencrypted Amazon EBS storage

Each finding includes supporting evidence, risk analysis, and recommended remediation.

---

## Tools Used

* AWS Inspector
* Amazon EC2
* Amazon EBS
* AWS Security Groups
* Nmap
* SQLite
* OWASP Juice Shop

---

## Frameworks

* OWASP Top 10 
* CIS AWS Foundations Benchmark
* AWS Security Best Practices

---

## Skills Demonstrated

* Cloud Security Assessment
* Vulnerability Assessment
* Risk Analysis
* Network Security
* Secure Configuration Review
* Application Security Testing
* Security Hardening
* Security Documentation
