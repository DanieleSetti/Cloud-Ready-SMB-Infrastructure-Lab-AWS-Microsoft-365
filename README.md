# Cloud-SMB-Infrastructure-Lab-AWS-Microsoft-365
# Cloud-Ready SMB Infrastructure Lab

**Platform: AWS + Microsoft 365**
**Scope: Cloud Infrastructure, Identity Integration, Operational Automation**

---

## 📌 Project Overview

This lab simulates a realistic IT modernization scenario for a small-to-medium-sized business (SMB) looking to adopt cloud technologies and enterprise collaboration platforms. The objective is to demonstrate hands-on competence in system administration and basic cloud operations within AWS and Microsoft 365 ecosystems.

All configurations and deployments were performed manually — replicating the daily workflow expected from a junior system administrator or cloud engineer in a managed service provider (MSP) or consulting context.

---

## 🎯 Project Objectives

* **Deploy and secure core infrastructure components in AWS**
  Including EC2, S3, IAM roles, and a scripted backup process.

* **Simulate integration with Microsoft 365 services**
  Focusing on identity management, email, file collaboration, and endpoint access.

* **Implement basic monitoring, documentation, and automation**
  To ensure lifecycle maintenance and operational resilience.

---

This project demonstrates my ability to independently manage essential sysadmin and cloud tasks — similar to the responsibilities expected at companies like **Eleva**, where engineers operate across infrastructure, end-user tools, and security platforms.

---

## 🏗 AWS Infrastructure Deployment

This section details the setup of a minimal, production-style AWS environment using Free Tier services. The goal was to deploy a secure, web-accessible EC2 instance, configure an S3-based backup routine, and implement automation logic.

---

### 1. Log into AWS Console

Access the [AWS EC2 Dashboard](https://console.aws.amazon.com/ec2) and choose a Free Tier–eligible region (e.g., **eu-central-1 / Frankfurt**) to avoid charges.

---

### 2. Launch an EC2 Instance

* **Instance name**: `ubuntu-free-tier`
* **AMI**: Ubuntu Server 22.04 LTS (Free Tier eligible)
* **Instance type**: `t2.micro` or `t3.micro`
  (1 vCPU, 1 GB RAM — both Free Tier eligible)
* **Key pair**: Create and download a `.pem` key (e.g., `my-ec2-key`)

---

### 3. Configure Networking & Security

* **VPC/Subnet**: Use default
* **Security Group**:

  * Allow SSH (port 22) from **your IP only**
  * Optional: Allow HTTP (port 80) from **0.0.0.0/0** for web hosting

---

### 4. Configure Storage

* **Volume**: Default 8 GiB SSD
  (Free Tier includes up to 30 GiB of EBS)

---

### 5. Create Instance via AWS CLI (Optional)

```bash
aws ec2 run-instances \
  --image-id ami-xxxxxxxxxxxxxxxxx \
  --instance-type t2.micro \
  --key-name my-ec2-key \
  --block-device-mappings '[{"DeviceName":"/dev/sda1","Ebs":{"VolumeSize":8}}]' \
  --network-interfaces '[{"AssociatePublicIpAddress":true,"DeviceIndex":0,"Groups":["sg-xxxxxxx"]}]' \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=ubuntu-free-tier}]' \
  --count 1
```

---

### 6. SSH into Instance

```bash
ssh -i "my-ec2-key.pem" ubuntu@<public-ip>
```

---

### 7. Install Web Service (Nginx)

```bash
sudo apt update && sudo apt install nginx -y
echo "<h1>Welcome to SMB Cloud Infra</h1>" | sudo tee /var/www/html/index.html
sudo systemctl enable nginx
```

Visit your EC2 public IP in a browser to confirm deployment.

---

### 8. Create S3 Bucket

Create a bucket in the AWS Console (e.g., `smb-backup-bucket`).
Test upload with:

```bash
aws s3 cp test.txt s3://smb-backup-bucket/
```

---

### 9. Configure EC2 Access to S3

1. **Create IAM Role**

   * Service: EC2
   * Policy: `AmazonS3FullAccess` (or a fine-tuned policy)
   * Role name: `EC2-S3-Access`

2. **Attach Role to EC2 Instance**

   * EC2 Console → Actions → Security → Modify IAM Role

---

### 10. Install AWS CLI on EC2

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip -y
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

If IAM role is configured properly, no `aws configure` is needed.

---

### 11. Automate Backups via Cron

**Script: `/home/smbadmin/backup-to-s3.sh`**

```bash
#!/bin/bash
TIMESTAMP=$(date +%F-%H-%M)
ARCHIVE="/tmp/nginx-backup-$TIMESTAMP.tar.gz"

tar -czf $ARCHIVE /etc/nginx /var/log/nginx
aws s3 cp $ARCHIVE s3://smb-backup-bucket/
echo "$TIMESTAMP backup complete" >> /var/log/backup.log
rm -f $ARCHIVE
```

```bash
chmod +x /home/smbadmin/backup-to-s3.sh
sudo crontab -e
```

Add:

```bash
0 2 * * * /home/smbadmin/backup-to-s3.sh
```

---

## ✅ Outcome Summary

| Component     | Status                      |
| ------------- | --------------------------- |
| EC2 instance  | Launched and secured        |
| Web server    | Nginx configured, reachable |
| S3 bucket     | Created and integrated      |
| IAM role      | Configured for EC2 → S3     |
| AWS CLI       | Installed and tested        |
| Daily backups | Automated via cron job      |

---

Certainly, here's a rewritten and more professional version of your GitHub Pages (Gitpage) documentation entry, adapted to match the tone, structure, and style suitable for technical portfolios or case study documentation:

---

# Microsoft 365 + SaaS Infrastructure Lab

*Enterprise Simulation for User & Access Management, Communication, and Document Collaboration*

## 🎯 Objective

The purpose of this lab is to simulate a basic corporate IT environment using **Microsoft 365 as a SaaS platform**. The focus areas include:

* Email communication
* Document storage and collaboration
* User lifecycle and role management
* Multi-Factor Authentication (MFA) and security policies via Azure Active Directory (Entra)

This project demonstrates proficiency with **Microsoft 365 cloud services**, **Azure AD integration**, and **modern security practices**—essential skills for enterprise or SMB roles in system administration, cloud IT, or IT support.

---

## 📝 Status (as of 2025-05-07)

The **Microsoft 365 Developer Program account** was successfully created. However, **sandbox provisioning is currently on hold** due to Microsoft's eligibility review:

> *"You don’t currently qualify for a Microsoft 365 Developer Program sandbox subscription."*

As sandbox access is required for full implementation, **this portion of the lab is currently paused**. Once access is granted, development will resume accordingly.

Note: This project was requested by **Eleva**, with a specific requirement to use Microsoft 365 and AWS (no self-hosted alternatives).

---

## ✅ Prepared & Documented Steps

### 1. 🧩 Microsoft 365 Developer Environment Setup

**Platform:** [Microsoft 365 Developer Program (Free)](https://developer.microsoft.com/en-us/microsoft-365/dev-program)

**Steps Taken:**

* Registered for the M365 Developer Program
* Attempted to provision an *Instant Sandbox* environment:

  * **Region:** EU
  * **Domain:** `daniele365.onmicrosoft.com`
  * **Admin User:** `admin@daniele365.onmicrosoft.com`

> 🔐 *Password for the global admin was securely saved.*

If provisioned, the following portals would be accessible:

| Tool                | URL                                                            |
| ------------------- | -------------------------------------------------------------- |
| Microsoft 365 Admin | [https://admin.microsoft.com](https://admin.microsoft.com)     |
| Azure AD (Entra)    | [https://entra.microsoft.com](https://entra.microsoft.com)     |
| Outlook Web Access  | [https://outlook.office365.com](https://outlook.office365.com) |

**Documentation File:** `m365-setup-notes.md`

```md
# Microsoft 365 Dev Tenant Setup
- Domain: daniele365.onmicrosoft.com
- Admin User: admin@daniele365.onmicrosoft.com
- Region: EU
- Creation Date: 2025-05-07
```

---

### 2. 👥 User Creation (Manual via M365 Admin Portal)

**Location:** `Users > Active Users` in the M365 Admin Center.

| Name  | Role                 | Email                                                                       | Password        |
| ----- | -------------------- | --------------------------------------------------------------------------- | --------------- |
| Admin | Global Administrator | [admin@daniele365.onmicrosoft.com](mailto:admin@daniele365.onmicrosoft.com) | \[Saved]        |
| User  | Standard User        | [user@daniele365.onmicrosoft.com](mailto:user@daniele365.onmicrosoft.com)   | `User@2025`     |
| Guest | External B2B Guest   | [guest@gmail.com](mailto:guest@gmail.com)                                   | Invitation only |

**Access Permissions:**

* **Admin:** Full global admin rights
* **User:** Licensed M365 user
* **Guest:** External B2B guest; no license; link-based access only

*Update in notes file:*

```md
## Users
- Admin (Global Admin)
- User (Standard)
- Guest (External)
```

---

### 3. 📧 Email Verification (Outlook Web)

**Login:** [https://outlook.office365.com](https://outlook.office365.com) with `user@daniele365.onmicrosoft.com`

**Checklist:**

* [ ] Send a test email to `admin@...` and confirm delivery
* [ ] Test calendar scheduling
* [ ] Attach and download `.docx` documents to validate file exchange

---

### 4. 🗂️ SharePoint Team Site + Guest Access

**Navigation:** M365 Admin Portal → **Apps > SharePoint > Open**

**Site Configuration:**

* **Type:** Team Site
* **Name:** IT-Docs
* **Members:**

  * `user@daniele365.onmicrosoft.com`
  * `guest@gmail.com` (invited via email)

**Functionality Tests:**

* [ ] Upload `infra-plan.docx`
* [ ] Ensure internal user has view/edit access
* [ ] Share file with guest — confirm restricted, link-based access

---

### 5. 🔐 Multi-Factor Authentication & Baseline Security

**Tool:** [Microsoft Entra](https://entra.microsoft.com)

**Actions:**

* Enable **MFA** for `user@daniele365.onmicrosoft.com`

  * Test with **Microsoft Authenticator App**
* Activate **Security Defaults**

  * Navigate to *Entra → Properties → Manage Security Defaults*
  * Enables:

    * Mandatory MFA for all users
    * Legacy auth protocol deprecation
    * Baseline conditional access enforcement

---

### 6. 🛠️ Monitoring & Secure Access (AWS + Netdata)

**Monitoring Tool:** [Netdata](https://www.netdata.cloud)

**Installation Script:**

```bash
bash <(curl -L -Ss https://my-netdata.io/kickstart.sh)
```

**Access Control:**

* AWS Security Group configured to expose port **19999** (*Netdata dashboard*) only to the developer's public IP address.
