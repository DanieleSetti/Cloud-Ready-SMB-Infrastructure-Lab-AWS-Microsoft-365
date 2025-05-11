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

## 🔒 Next Steps / Optional Add-ons

* Configure CloudWatch for deeper monitoring
* Replace `AmazonS3FullAccess` with a principle-of-least-privilege policy
* Encrypt backup data before upload
* Introduce log rotation for Nginx and backup logs

---

Would you like a matching GitHub Actions badge or diagram to visualize this setup on your GitPage?
