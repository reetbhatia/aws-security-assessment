# ☁️ AWS Security Assessment

**What happens after you discover critical security weaknesses in a cloud environment?**

This project answers that question.

I performed a security assessment of an **AWS-hosted OWASP Juice Shop** environment to identify vulnerabilities across the application, infrastructure, network, and data layers. I extended the project by designing a **disaster recovery strategy** to improve availability, reduce downtime, and strengthen recovery capabilities.

Together, these reports demonstrate both sides of cloud security: **identifying risk** and **designing resilient solutions**.

---

## Explore the Reports

📄 **[Cloud Security Assessment](cloud-security-assessment.md)**
Identifies security weaknesses, validates their impact with supporting evidence, and provides remediation recommendations mapped to the **OWASP Top 10** and **CIS AWS Foundations Benchmark**.

📄 **[Disaster Recovery Strategy](disaster-recovery-strategy.md)**
Builds on the assessment by proposing a resilient AWS architecture featuring Multi-AZ deployments, automated backups, Route 53 failover, and Cross-Region Replication to support **RTO < 15 minutes** and **RPO < 5 minutes**.

---

## Highlights

* Assessed security across AWS infrastructure, networking, application, and data layers.
* Validated findings through AWS Inspector, manual testing, and network analysis.
* Prioritized remediation using industry security frameworks.
* Designed a multi-region disaster recovery strategy to improve resilience and business continuity.

---

## Technologies

**AWS:** EC2, RDS, S3, EBS, AWS Backup, Route 53, Lambda, CloudFormation, CloudWatch, CloudTrail, AWS Config, AWS Inspector

**Security:** OWASP Juice Shop, Nmap, SQLite

**Frameworks:** OWASP Top 10 (2021), CIS AWS Foundations Benchmark, AWS Well-Architected Framework

---

