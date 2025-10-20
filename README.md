
# 🚀 Launching, Listing, and Deleting Amazon EC2 Instances using AWS CLI

![AWS EC2 Banner](https://github.com/awsdocs/amazon-ec2-user-guide/blob/main/doc_source/images/EC2_Banner.png?raw=true)

Use the **AWS Command Line Interface (AWS CLI)** to easily **launch**, **list**, and **delete** Amazon Elastic Compute Cloud (**EC2**) instances.

> ⚠️ **Note:**  
> If you launch an instance that isn’t within the AWS Free Tier, you’ll be billed as soon as the instance starts — even if it’s idle.  
> For additional examples, visit the [AWS CLI Reference Guide](https://docs.aws.amazon.com/cli/latest/reference/ec2/).

---

## 📋 Table of Contents
- [🧩 Prerequisites](#-prerequisites)
- [🚀 Launch Your Instance](#-launch-your-instance)
- [💽 Add a Block Device](#-add-a-block-device)
- [🏷️ Add a Tag to Your Instance](#️-add-a-tag-to-your-instance)
- [🔌 Connect to Your Instance](#-connect-to-your-instance)
- [📜 List Your Instances](#-list-your-instances)
- [💣 Delete (Terminate) Your Instance](#-delete-terminate-your-instance)
- [📚 References](#-references)

---

## 🧩 Prerequisites

Before running EC2 commands, ensure you have:

✅ Installed and configured the **AWS CLI**  
> See: [Installing AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

✅ Proper **IAM permissions** for EC2 access  
> See: [IAM Policies for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html)

✅ A **key pair** and **security group**  

✅ A selected **Amazon Machine Image (AMI)** and its **AMI ID**  
> See: [Finding a Suitable AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html)

---

## 🚀 Launch Your Instance

To launch an EC2 instance, run:

```bash
aws ec2 run-instances \
  --image-id ami-xxxxxxxx \
  --count 1 \
  --instance-type t2.micro \
  --key-name MyKeyPair \
  --security-group-ids sg-903004f8 \
  --subnet-id subnet-6e7f829e
````

🕒 Your instance will first appear as **pending**, then become **running** after a few minutes.

<details>
<summary>🧾 Example JSON Output</summary>

```json
{
  "OwnerId": "123456789012",
  "Instances": [
    {
      "InstanceId": "i-5203422c",
      "ImageId": "ami-173d747e",
      "InstanceType": "t2.micro",
      "State": { "Code": 0, "Name": "pending" },
      "PrivateIpAddress": "10.0.1.114",
      "KeyName": "MyKeyPair",
      "SubnetId": "subnet-6e7f829e",
      "VpcId": "vpc-1a2b3c4d"
    }
  ]
}
```

</details>

---

## 💽 Add a Block Device

Attach extra Amazon EBS volumes using `--block-device-mappings`.

**Example 1 — Add 20GB EBS volume:**

```bash
--block-device-mappings "[{\"DeviceName\":\"/dev/sdf\",\"Ebs\":{\"VolumeSize\":20,\"DeleteOnTermination\":false}}]"
```

**Example 2 — From Snapshot:**

```bash
--block-device-mappings "[{\"DeviceName\":\"/dev/sdf\",\"Ebs\":{\"SnapshotId\":\"snap-a1b2c3d4\"}}]"
```

**Example 3 — Two ephemeral volumes:**

```bash
--block-device-mappings "[{\"DeviceName\":\"/dev/sdf\",\"VirtualName\":\"ephemeral0\"},{\"DeviceName\":\"/dev/sdg\",\"VirtualName\":\"ephemeral1\"}]"
```

> 📘 For more: [Block Device Mapping in EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/block-device-mapping-concepts.html)

---

## 🏷️ Add a Tag to Your Instance

Use tags to identify or organize your instances.

```bash
aws ec2 create-tags \
  --resources i-5203422c \
  --tags Key=Name,Value=MyInstance
```

> 💡 Tip: Tags help categorize instances by environment, project, or owner.

---

## 🔌 Connect to Your Instance

Once your instance is **running**, connect using SSH (for Linux) or RDP (for Windows).
See: [Connect to Your EC2 Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)

---

## 📜 List Your Instances

### List All Instances:

```bash
aws ec2 describe-instances
```

### Filter by Instance Type:

```bash
aws ec2 describe-instances \
  --filters "Name=instance-type,Values=t2.micro" \
  --query "Reservations[].Instances[].InstanceId"
```

**Example Output:**

```json
["i-05e998023d9c69f9a"]
```

### Filter by Tag:

```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=MyInstance"
```

### Filter by AMI IDs:

```bash
aws ec2 describe-instances \
  --filters "Name=image-id,Values=ami-x0123456,ami-y0123456,ami-z0123456"
```

---

## 💣 Delete (Terminate) Your Instance

⚠️ **Terminating an instance is irreversible.**

```bash
aws ec2 terminate-instances --instance-ids i-5203422c
```

<details>
<summary>🧾 Example Output</summary>

```json
{
  "TerminatingInstances": [
    {
      "InstanceId": "i-5203422c",
      "CurrentState": { "Code": 32, "Name": "shutting-down" },
      "PreviousState": { "Code": 16, "Name": "running" }
    }
  ]
}
```

</details>

> 💡 Tip: If you plan to reconnect later, use `stop-instances` instead of `terminate-instances`.

---

## 📚 References

* 🪣 [Amazon EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/)
* 💻 [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)
* 🔐 [IAM Policies for EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html)

---

⭐ **If you found this helpful, consider starring this repo!**
Made with 💛 by [YourName](https://github.com/YourUsername)

```

---

Would you like me to include **badges (e.g., AWS Certified, Built with Bash, License, etc.)** and **GitHub-style banners (like shields.io)** to make it look more professional and polished?
```
