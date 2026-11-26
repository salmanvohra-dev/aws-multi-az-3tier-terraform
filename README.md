# 🏗️ Enterprise Multi-AZ 3-Tier AWS Infrastructure Automation (Terraform)

## 📌 Project Overview

A production-grade, highly available **Multi-AZ 3-Tier Enterprise Cloud Architecture** on **AWS**, provisioned end-to-end using **Modular Terraform** and automated via **GitHub Actions CI/CD**. Designed for fault tolerance, strict network isolation, and zero-drift state management, this infrastructure separates public ingress, private compute workloads, and isolated data tiers across multiple Availability Zones.

The platform implements **S3 + DynamoDB** remote state locking for safe team collaboration, **VPC Flow Logs** for real-time network traffic auditing, and automated **AWS Backup** vaults for disaster recovery compliance.

---

## 🗺️ System Architecture

<p align="center">
  <img width="1376" height="768" alt="AWS Multi-AZ 3-Tier Enterprise Architecture" src="YOUR_GITHUB_IMAGE_LINK_HERE" />
</p>

---

## 🛠️ Tech Stack & AWS Services

| Layer | Technologies & Services Used |
| :--- | :--- |
| **Infrastructure as Code** | Modular Terraform (`vpc`, `security`, `compute`, `database`, `backup`) |
| **State Management** | Amazon S3 (Encrypted Remote Backend) + DynamoDB (State Locking) |
| **Networking & Edge** | Amazon VPC, Public/Private/Isolated Subnets (Multi-AZ), Internet Gateway, NAT Gateways |
| **Compute & Load Balancing** | Application Load Balancer (ALB), EC2 Auto Scaling Groups (ASG), Bastion / SSM Session Manager |
| **Security & Governance** | Least-Privilege IAM Roles, Stateful Security Groups, NACLs, VPC Flow Logs (CloudWatch) |
| **Disaster Recovery** | AWS Backup (Automated Scheduled Snapshots & Retention Policies) |
| **CI/CD Automation** | GitHub Actions (`terraform fmt`, `validate`, `plan` on PR, `apply` on merge) |

---

## ⚡ Key Architectural Highlights

* **Strict 3-Tier Network Isolation:**
  * **Public Tier:** Houses the Application Load Balancer (ALB) and redundant Multi-AZ NAT Gateways for outbound patching.
  * **Private Application Tier:** Runs EC2 instances inside an Auto Scaling Group with zero direct internet exposure.
  * **Isolated Data Tier:** Dedicated private subnets without internet or NAT routes for database workloads, accessible only from the App Tier security group.
* **Modular & Reusable Terraform Design:** Built with decoupled, environment-agnostic Terraform modules (`dev`, `staging`, `prod`) using `terraform.tfvars` and strict input validation.
* **Bulletproof Remote State & CI/CD:** Configured S3 bucket versioning with server-side encryption (SSE-KMS) and DynamoDB locking to prevent concurrent state corruption during automated GitHub Actions runs.
* **Observability & Automated Backup:** Enabled VPC Flow Logs streaming to CloudWatch Logs for security auditing and configured AWS Backup plans with automated daily snapshots.

---

## 🔥 Infrastructure Engineering & Troubleshooting Highlights

| # | Scenario / Challenge | Root Cause | Resolution |
| :--- | :--- | :--- | :--- |
| **1** | **Terraform State Lock Contention** | Concurrent CI/CD runs attempted simultaneous state modifications on the same workspace. | Configured DynamoDB `LockID` table with S3 backend and serialized GitHub Actions concurrency groups. |
| **2** | **Asymmetric Routing & NAT Failover** | Single NAT Gateway created a single point of failure and cross-AZ data transfer costs. | Deployed dedicated per-AZ NAT Gateways with isolated private route tables per Availability Zone. |
| **3** | **VPC Teardown `DependencyViolation`** | Residual Elastic Network Interfaces (ENIs) and ALB target attachments blocked `terraform destroy`. | Added explicit `create_before_destroy` lifecycle rules and ordered resource dependencies cleanly. |

---

## 📂 Repository Structure

```text
├── .github/workflows/       # Automated Terraform Plan & Apply CI/CD pipelines
├── modules/
│   ├── vpc/                 # Multi-AZ VPC, Subnets, IGW, NAT Gateways, Route Tables, Flow Logs
│   ├── security/            # Tiered Security Groups & Least-Privilege IAM Instance Profiles
│   ├── compute/             # Application Load Balancer, Target Groups & Auto Scaling Group
│   └── backup/              # AWS Backup Vault, Backup Plan & Resource Selection
├── main.tf                  # Root module orchestration
├── variables.tf             # Parameterized input definitions
├── outputs.tf               # ALB DNS, VPC ID, Subnet IDs & ARN exports
└── backend.tf               # S3 + DynamoDB remote state configuration
