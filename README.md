# 🚀 Launching, Listing, and Deleting Amazon EC2 Instances with AWS CLI

![AWS](https://img.shields.io/badge/AWS-EC2-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![CLI](https://img.shields.io/badge/AWS-CLI-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)

> A comprehensive guide to managing Amazon EC2 instances using the AWS Command Line Interface

## 📋 Table of Contents

- [Overview](#-overview)
- [Prerequisites](#-prerequisites)
- [Launch Instance](#-launch-instance)
- [Add Block Device](#-add-block-device)
- [Tag Management](#-tag-management)
- [Connect to Instance](#-connect-to-instance)
- [List Instances](#-list-instances)
- [Delete Instance](#-delete-instance)
- [References](#-references)

---

## 🌟 Overview

This guide demonstrates how to use the AWS CLI to manage Amazon EC2 instances efficiently. You can launch, list, and terminate instances directly from your command line.

> **⚠️ Cost Warning:** If you launch an instance outside the AWS Free Tier, you'll be billed from the moment it launches, even if idle.

For additional command examples, check the [AWS CLI Reference Guide](https://docs.aws.amazon.com/cli/latest/).

---

## ✅ Prerequisites

Before running EC2 commands, ensure you have:

### 1️⃣ AWS CLI Installation
Install and configure the AWS CLI on your system.
- 📖 [Installing or updating AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- 🔐 [Authentication and access credentials](https://docs.aws.amazon.com/cli/latest/userguide/cli-authentication.html)

### 2️⃣ IAM Permissions
Set up IAM permissions for Amazon EC2 access.
- 📖 [IAM policies for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html)

### 3️⃣ Security Setup
- 🔑 Create a **key pair** for SSH access
- 🛡️ Create a **security group** for network rules

### 4️⃣ AMI Selection
Select an Amazon Machine Image (AMI) and note its ID.
- 📖 [Finding a suitable AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html)

---

## 🚀 Launch Instance

Launch an Amazon EC2 instance into a VPC using the `run-instances` command.

### Basic Launch Command

```bash
aws ec2 run-instances \
  --image-id ami-xxxxxxxx \
  --count 1 \
  --instance-type t2.micro \
  --key-name MyKeyPair \
  --security-group-ids sg-903004f8 \
  --subnet-id subnet-6e7f829e
```

### 📊 Instance States

| State | Description |
|-------|-------------|
| `pending` | Instance is initializing |
| `running` | Instance is active and ready |
| `shutting-down` | Instance is terminating |
| `terminated` | Instance is deleted |

### Example Response

<details>
<summary>Click to expand JSON response</summary>

```json
{
    "OwnerId": "123456789012",
    "ReservationId": "r-5875ca20",
    "Instances": [
        {
            "InstanceId": "i-5203422c",
            "ImageId": "ami-173d747e",
            "State": {
                "Code": 0,
                "Name": "pending"
            },
            "InstanceType": "t2.micro",
            "KeyName": "MyKeyPair",
            "LaunchTime": "2013-07-19T02:42:39.000Z",
            "Placement": {
                "AvailabilityZone": "us-west-2b"
            }
        }
    ]
}
```

</details>

---

## 💾 Add Block Device

Add additional EBS volumes or instance store volumes when launching instances using block device mapping.

### Option 1: Standard EBS Volume (20 GB)

```bash
--block-device-mappings '[{
  "DeviceName":"/dev/sdf",
  "Ebs":{
    "VolumeSize":20,
    "DeleteOnTermination":false
  }
}]'
```

### Option 2: Volume from Snapshot

```bash
--block-device-mappings '[{
  "DeviceName":"/dev/sdf",
  "Ebs":{
    "SnapshotId":"snap-a1b2c3d4"
  }
}]'
```

### Option 3: Multiple Volumes

```bash
--block-device-mappings '[
  {"DeviceName":"/dev/sdf","VirtualName":"ephemeral0"},
  {"DeviceName":"/dev/sdg","VirtualName":"ephemeral1"}
]'
```

### Option 4: Empty Mapping (No Volume)

```bash
--block-device-mappings '[{
  "DeviceName":"/dev/sdj",
  "NoDevice":""
}]'
```

📖 **Learn more:** [Block Device Mapping](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/block-device-mapping-concepts.html)

---

## 🏷️ Tag Management

Tags are metadata labels that help organize and manage your AWS resources.

### Add a Tag

```bash
aws ec2 create-tags \
  --resources i-5203422c \
  --tags Key=Name,Value=MyInstance
```

### Common Tag Examples

| Key | Value | Purpose |
|-----|-------|---------|
| `Name` | `WebServer-01` | Instance identification |
| `Environment` | `Production` | Environment classification |
| `Project` | `MyApp` | Project association |
| `Owner` | `TeamA` | Ownership tracking |

📖 **Learn more:** [Tagging Your Resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)

---

## 🔌 Connect to Instance

Once your instance is running, you can connect to it via SSH or RDP.

### For Linux Instances (SSH)

```bash
ssh -i /path/to/MyKeyPair.pem ec2-user@<public-ip-address>
```

### For Windows Instances (RDP)

Use Remote Desktop Protocol with the administrator password retrieved from the console.

📖 **Learn more:** [Connect to Your Amazon EC2 Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html)

---

## 📝 List Instances

Query and filter your EC2 instances using the `describe-instances` command.

### List All Instances

```bash
aws ec2 describe-instances
```

### Filter by Instance Type

```bash
aws ec2 describe-instances \
  --filters "Name=instance-type,Values=t2.micro" \
  --query "Reservations[].Instances[].InstanceId"
```

**Output:**
```json
[
    "i-05e998023d9c69f9a"
]
```

### Filter by Tag

```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=MyInstance"
```

### Filter by AMI ID

```bash
aws ec2 describe-instances \
  --filters "Name=image-id,Values=ami-x0123456,ami-y0123456,ami-z0123456"
```

### 🔍 Common Query Patterns

| Filter | Command |
|--------|---------|
| By instance state | `--filters "Name=instance-state-name,Values=running"` |
| By VPC | `--filters "Name=vpc-id,Values=vpc-12345678"` |
| By availability zone | `--filters "Name=availability-zone,Values=us-east-1a"` |

---

## 🗑️ Delete Instance

Terminate EC2 instances when they're no longer needed.

> **⚠️ CRITICAL WARNING:**
> - Termination is **permanent** and **irreversible**
> - All data on instance store volumes will be **lost**
> - EBS volumes with `DeleteOnTermination=true` will be **deleted**
> - **Back up your data** before terminating

### Terminate Command

```bash
aws ec2 terminate-instances --instance-ids i-5203422c
```

### Example Response

```json
{
    "TerminatingInstances": [
        {
            "InstanceId": "i-5203422c",
            "CurrentState": {
                "Code": 32,
                "Name": "shutting-down"
            },
            "PreviousState": {
                "Code": 16,
                "Name": "running"
            }
        }
    ]
}
```

### 💡 Alternative: Stop Instead of Terminate

To keep your instance for later use without charges:

```bash
aws ec2 stop-instances --instance-ids i-5203422c
```

📖 **Learn more:** [Terminate Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html)

---

## 📚 References

- [AWS CLI Command Reference - EC2](https://docs.aws.amazon.com/cli/latest/reference/ec2/)
- [Amazon EC2 User Guide](https://docs.aws.amazon.com/ec2/index.html)
- [AWS CLI Documentation](https://docs.aws.amazon.com/cli/)
- [EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)
- [AWS Free Tier](https://aws.amazon.com/free/)

---

## 📄 License

This documentation is provided as-is for educational purposes.

## 🤝 Contributing

Feel free to submit issues and enhancement requests!

---

<div align="center">

**Made with ❤️ for AWS CLI Users**

⭐ Star this repo if you found it helpful!

</div>
```

---

Would you like me to include **badges (e.g., AWS Certified, Built with Bash, License, etc.)** and **GitHub-style banners (like shields.io)** to make it look more professional and polished?
```
