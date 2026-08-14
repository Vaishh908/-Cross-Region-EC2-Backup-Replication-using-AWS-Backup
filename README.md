# Cross-Region EC2 Backup Replication using AWS Backup

---

# Project Description

Cross-Region EC2 Backup Replication using AWS Backup is a disaster recovery solution designed to protect an Amazon EC2 instance by automatically creating scheduled backups and replicating them across AWS regions. In this project, a Linux-based t3.micro EC2 instance is deployed in the US East (N. Virginia) region (us-east-1), which acts as the primary region. AWS Backup is configured with a backup plan named EC2-Cross-Region-Backup-Plan and a daily backup rule named Daily-EC2-Backup. The backups are stored in the EC2-Primary-Backup-Vault with a 7-day retention period. AWS Backup then automatically copies the recovery point to the EC2-DR-Backup-Vault in the US West (N. California) region (us-west-1). The successful completion of backup jobs and the presence of recovery points in both regions verify that cross-region backup replication is working correctly. This architecture provides an additional layer of protection against EC2 instance failure, data loss, or a disruption in the primary AWS region, while allowing the replicated recovery point in the secondary region to be used for disaster recovery and restoration.

---

# Prerequisites

Before starting the project, ensure the following requirements are available:

- AWS account with access to the AWS Management Console
- Linux EC2 instance (t3.micro)
- EC2 key pair for secure access
- Amazon EBS volume attached to the EC2 instance
- IAM permissions to use AWS Backup and EC2
- AWS Backup service enabled
- Two AWS regions:
   - Primary: US East (N. Virginia) — us-east-1
   - DR: US West (N. California) — us-west-1
- Basic knowledge of EC2, EBS, IAM, and AWS Backup
- Internet access for accessing the AWS Management Console

---

# Architecture Diagram

<img width="1536" height="1024" alt="ChatGPT Image Aug 12, 2026, 06_51_29 PM" src="https://github.com/user-attachments/assets/f96042f5-808b-4490-809e-b7f036f1a5f8" />

---

# Technologies Used

| Technology        | Purpose                       |
| ----------------- | ----------------------------- |
| Amazon EC2        | Source server                 |
| AWS Backup        | Backup management             |
| Amazon EBS        | EC2 storage backup            |
| AWS Backup Vault  | Backup storage                |
| IAM               | AWS Backup permissions        |
| Backup Plan       | Backup automation             |
| Backup Rule       | Backup scheduling             |
| Cross-Region Copy | Disaster recovery replication |
| Recovery Point    | Restore point                 |
| Linux             | EC2 operating system          |

---
# Installation Steps

This project is implemented using AWS Management Console services. The solution does not require any additional application framework or local software installation because AWS Backup provides the backup scheduling, backup storage, and cross-region replication functionality.

### Step 1: Launch the EC2 Instance

1. Sign in to the AWS Management Console.
2. Open the **Amazon EC2** service.
3. Select the primary AWS Region:
   `us-east-1 (US East - N. Virginia)`
4. Launch a Linux-based EC2 instance.
5. Configure the instance using the required settings:
   - Instance Type: `t3.micro`
   - Architecture: 64-bit
   - Operating System: Ubuntu/Linux
   - Storage: Amazon EBS
   - Key Pair: Configured key pair
6. Launch the instance.
7. Verify that the EC2 instance is in the `Running` state.

### Step 2: Prepare the EC2 Instance

1. Connect to the EC2 instance using SSH.
2. Verify that the instance is accessible.
3. Create or upload sample files if required for backup testing.
4. Confirm that the EC2 instance is functioning correctly before configuring AWS Backup.

### Step 3: Open AWS Backup

1. Open the **AWS Backup** service from the AWS Management Console.
2. Make sure the selected region is:
   `us-east-1`
3. Open **Backup vaults**.

### Step 4: Create the Primary Backup Vault

1. Select **Create backup vault**.
2. Enter the backup vault name:

   `EC2-Primary-Backup-Vault`

3. Keep the required default settings.
4. Create the backup vault.
5. Verify that the vault is available in `us-east-1`.

The primary backup vault is used to store the EC2 recovery point in the source region.

### Step 5: Create the Destination Backup Vault

1. Change the AWS Region to:
   `us-west-1 (US West - N. California)`
2. Open **AWS Backup**.
3. Select **Backup vaults**.
4. Select **Create backup vault**.
5. Enter:

   `EC2-DR-Backup-Vault`

6. Create the vault.
7. Verify that the destination vault is available in `us-west-1`.

This vault is used to store the replicated recovery point for disaster recovery.

### Step 6: Create the Backup Plan

1. Switch back to the primary region:
   `us-east-1`
2. Open **AWS Backup**.
3. Select **Backup plans**.
4. Select **Create backup plan**.
5. Create a new backup plan.
6. Enter the backup plan name:

   `EC2-Cross-Region-Backup-Plan`

7. Create the backup plan.

### Step 7: Configure the Backup Rule

Create a backup rule inside the backup plan.

Use the following configuration:

- Backup Rule Name:
  `Daily-EC2-Backup`
- Backup Frequency:
  `Daily`
- Backup Vault:
  `EC2-Primary-Backup-Vault`
- Time Zone:
  `Asia/Calcutta (UTC+05:30)`
- Retention:
  `7 days`

The backup rule automatically creates an EC2 recovery point according to the configured schedule.

### Step 8: Configure Cross-Region Backup Copy

Inside the `Daily-EC2-Backup` rule:

1. Locate the **Copy to destination** or cross-region copy configuration.
2. Enable the backup copy.
3. Select the destination Region:

   `us-west-1`

4. Select the destination backup vault:

   `EC2-DR-Backup-Vault`

5. Configure the copy retention period:

   `7 days`

6. Save the backup rule.

The configuration now creates the backup in the primary region and copies the recovery point to the destination region.

### Step 9: Assign the EC2 Instance

1. Open the backup plan:

   `EC2-Cross-Region-Backup-Plan`

2. Open the resource assignment section.
3. Create a resource assignment.
4. Select the resource type:

   `EC2`

5. Select the EC2 instance:

   `i-04cfe745666331faf`

6. Use the required AWS Backup IAM service role.
7. Save the resource assignment.

The EC2 instance is now protected by the backup plan.

### Step 10: Verify the Backup Plan

Before starting the backup, verify the complete configuration:

- Backup Plan:
  `EC2-Cross-Region-Backup-Plan`
- Backup Rule:
  `Daily-EC2-Backup`
- Source Region:
  `us-east-1`
- Primary Backup Vault:
  `EC2-Primary-Backup-Vault`
- Backup Frequency:
  Daily
- Retention:
  7 days
- Destination Region:
  `us-west-1`
- Destination Backup Vault:
  `EC2-DR-Backup-Vault`
- EC2 Resource:
  `i-04cfe745666331faf`

### Step 11: Trigger an On-Demand Backup

For immediate validation:

1. Open **AWS Backup**.
2. Select **Protected resources** or the EC2 resource.
3. Select the EC2 instance.
4. Choose **Create on-demand backup**.
5. Select:
   `EC2-Primary-Backup-Vault`
6. Configure the required retention period.
7. Start the backup.

This allows the backup configuration to be tested without waiting for the scheduled daily backup.

### Step 12: Monitor the Backup Job

1. Open:

   **AWS Backup → Jobs → Backup jobs**

2. Locate the EC2 backup job.
3. Monitor the job until it reaches:

   `Completed`

4. Verify that the backup job does not show an error.
5. Confirm that a recovery point has been created.

### Step 13: Verify the Primary Recovery Point

1. Open:

   **AWS Backup → Backup vaults**

2. Select:

   `EC2-Primary-Backup-Vault`

3. Open **Recovery points**.
4. Verify that the EC2 recovery point is available.

This confirms that the EC2 instance was successfully backed up in the primary region.

### Step 14: Verify the Cross-Region Copy Job

1. Open:

   **AWS Backup → Jobs → Copy jobs**

2. Locate the copy job associated with the EC2 backup.
3. Verify the source region:

   `us-east-1`

4. Verify the destination region:

   `us-west-1`

5. Wait until the copy job reaches:

   `Completed`

This confirms that the recovery point was successfully replicated to the secondary region.

### Step 15: Verify the Destination Recovery Point

1. Switch the AWS Region to:

   `us-west-1`

2. Open **AWS Backup**.
3. Select **Backup vaults**.
4. Open:

   `EC2-DR-Backup-Vault`

5. Select **Recovery points**.
6. Verify that the replicated EC2 recovery point is available.

The presence of the recovery point confirms successful cross-region backup replication.

### Step 16: Verify the Scheduled Backup

1. Return to the primary region:
   `us-east-1`
2. Open:

   **AWS Backup → Backup plans**

3. Open:

   `EC2-Cross-Region-Backup-Plan`

4. Open:

   `Daily-EC2-Backup`

5. Verify that the daily schedule is enabled.
6. Verify the configured retention period.
7. Verify that the cross-region copy configuration remains enabled.

The final configuration provides automated daily EC2 backup and cross-region replication.

---

# Project Structure

```text
Cross-Region-EC2-Backup-Replication-AWS-Backup/
│
├── README.md
│
├── architecture/
│   └── architecture-diagram.png
│
├── screenshots/
│   ├── 01-ec2-instance.png
│   ├── 02-primary-backup-vault.png
│   ├── 03-dr-backup-vault.png
│   ├── 04-backup-plan.png
│   ├── 05-backup-rule.png
│   ├── 06-cross-region-copy.png
│   ├── 07-resource-assignment.png
│   ├── 08-backup-job-completed.png
│   ├── 09-primary-recovery-point.png
│   ├── 10-copy-job-completed.png
│   ├── 11-dr-recovery-point.png
│   └── 12-scheduled-backup.png
│
└── .gitignore
```
---

# Implementation Steps

# Step 1 - Create EC2 Instance

Login to the AWS Management Console.

Search for:

EC2

Open the EC2 service.

Select:

Launch Instance

### Configure the instance.

| Setting       | Value             |
| ------------- | ----------------- |
| Name          | EC2-Backup-Source |
| AMI           | Ubuntu/Linux      |
| Instance Type | `t3.micro`        |
| Key Pair      | Project.pem       |
| Region        | `us-east-1`       |
| Storage       | Default EBS       |

Launch the instance.

<img width="1919" height="979" alt="Screenshot 2026-08-12 172000" src="https://github.com/user-attachments/assets/a2f255bd-22d3-4dc9-84c8-57594390680f" />

---

# Step 2 - Open AWS Backup

From the AWS Management Console, search:

AWS Backup

Make sure the selected region is:

US East (N. Virginia)
us-east-1

AWS Backup is used to automate the backup and cross-region replication process.

Create Primary Backup Vault

Navigate to:

- AWS Backup
- Backup vaults
- Create backup vault

Create the following vault:

EC2-Primary-Backup-Vault

Region:

us-east-1

The vault is used to store the EC2 backup recovery points in the primary region.

### Primary Vault

| Configuration | Value                      |
| ------------- | -------------------------- |
| Vault Name    | `EC2-Primary-Backup-Vault` |
| Region        | `us-east-1`                |
| Purpose       | Primary backup storage     |
| Resource      | EC2                        |

<img width="1920" height="1080" alt="Screenshot 2026-08-12 162854" src="https://github.com/user-attachments/assets/a36bf458-bb6b-4370-ba7d-c602547ed5da" />

---

# Step 3 - Create DR Backup Vault

Change the AWS region to:

US West (N. California)
us-west-1

Navigate to:

- AWS Backup
- Backup vaults
- Create backup vault

Create:

EC2-DR-Backup-Vault

### DR Vault

| Configuration | Value                 |
| ------------- | --------------------- |
| Vault Name    | `EC2-DR-Backup-Vault` |
| Region        | `us-west-1`           |
| Purpose       | Disaster recovery     |
| Backup Source | `us-east-1`           |

The DR vault is the destination for cross-region backup copies.

<img width="1920" height="1080" alt="Screenshot 2026-08-12 163101" src="https://github.com/user-attachments/assets/72164a6c-1be8-45be-9a70-3cfff807e65f" />

---

# Step 4 - Create Backup Plan

Return to:

US East (N. Virginia)
us-east-1

Open:

- AWS Backup
- Backup plans
- Create backup plan

Select:

Build a new plan

Use the following configuration.

Backup Plan
EC2-Cross-Region-Backup-Plan

<img width="1920" height="1080" alt="Screenshot 2026-08-12 164151" src="https://github.com/user-attachments/assets/9337b633-5724-4993-a61a-3cd8b66da083" />


---

# Step 5 - Configure Backup Rule

Create a backup rule:

Daily-EC2-Backup

### Backup Rule Configuration

| Setting           | Value                      |
| ----------------- | -------------------------- |
| Backup Rule Name  | `Daily-EC2-Backup`         |
| Frequency         | Daily                      |
| Backup Vault      | `EC2-Primary-Backup-Vault` |
| Time Zone         | Asia/Calcutta              |
| Retention         | 7 days                     |
| Cold Storage      | Disabled                   |
| Continuous Backup | Disabled                   |
| Malware Scan      | None                       |
| Backup Index      | Not indexed                |

Backup Schedule

The configured backup time is:

05:40 PM

Time zone:

Asia/Calcutta (UTC+05:30)

Backup frequency:

Daily

Start within:

1 hour

Complete within:

7 days

Therefore, the schedule appears as:

Daily at 05:40 PM
Asia/Calcutta (UTC+05:30)

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8579ab49-99b7-49ef-b007-160f4f724d34" />

---

# Step 6- Configure Cross-Region Copy

Inside the backup rule, locate:

Copy to destination

Enable cross-region copy.

Select:

Destination Region:
US West (N. California)
us-west-1

Select destination vault:

EC2-DR-Backup-Vault

Configure retention:

1 week / 7 days

### Copy Configuration

| Setting               | Value                      |
| --------------------- | -------------------------- |
| Source Region         | `us-east-1`                |
| Destination Region    | `us-west-1`                |
| Source Vault          | `EC2-Primary-Backup-Vault` |
| Destination Vault     | `EC2-DR-Backup-Vault`      |
| Retention             | 7 days                     |
| Cold Storage          | Disabled                   |
| Archive EBS Snapshots | Disabled                   |

Important: Duplicate CopyActions Error

During configuration, the following error may occur:

Duplicate CopyActions provided in backupRule.

This happens when more than one identical cross-region copy configuration exists in the backup rule.

Solution

Open:

AWS Backup
→ Backup Plans
→ EC2-Cross-Region-Backup-Plan
→ Daily-EC2-Backup
→ Edit

Check:

Copy to destination

Keep only one copy action:

us-west-1
EC2-DR-Backup-Vault

Remove any duplicate copy action.

Save the rule again.

<img width="1920" height="1080" alt="Screenshot 2026-08-12 163101" src="https://github.com/user-attachments/assets/aaa007b9-76bf-4653-8f54-cd0d0d800fc3" />

---

# Step 7 - Assign EC2 Resource

Open the backup plan:

EC2-Cross-Region-Backup-Plan

Select:

Assign resources
Resource Assignment

Use:

EC2-Backup-Assignment

IAM role:

Default role

AWS Backup will use:

AWSBackupDefaultServiceRole
Resource Selection

Select:

Include specific resource types

Resource type:

EC2

Choose the instance:

i-04cfe745666331faf

Do not add any exclusion.

The final assignment is:

- EC2-Backup-Assignment
- EC2
- i-04cfe745666331faf


<img width="1920" height="1080" alt="Screenshot 2026-08-12 164721" src="https://github.com/user-attachments/assets/fda09137-0265-469c-93d6-789714484afe" />

---

# Step 8 - Verify Backup Plan

After creating the backup plan, the plan should show:

EC2-Cross-Region-Backup-Plan

The backup rule should show:

Daily-EC2-Backup

The backup vault should show:

EC2-Primary-Backup-Vault

The destination should show:

EC2-DR-Backup-Vault

### Final Backup Plan Configuration :

| Configuration      | Value                        |
| ------------------ | ---------------------------- |
| Plan Name          | EC2-Cross-Region-Backup-Plan |
| Rule Name          | Daily-EC2-Backup             |
| Source Region      | us-east-1                    |
| Destination Region | us-west-1                    |
| Source Vault       | EC2-Primary-Backup-Vault     |
| Destination Vault  | EC2-DR-Backup-Vault          |
| Resource Type      | EC2                          |
| Instance           | i-04cfe745666331faf          |
| Frequency          | Daily                        |
| Backup Time        | 05:40 PM                     |
| Time Zone          | Asia/Calcutta                |
| Start Within       | 1 hour                       |
| Complete Within    | 7 days                       |
| Backup Retention   | 7 days                       |
| Copy Retention     | 7 days                       |

<img width="1920" height="1080" alt="Screenshot 2026-08-12 164151" src="https://github.com/user-attachments/assets/d8581d77-e39c-4a47-9cd0-74195b874e88" />

---

# Step 9 - Create On-Demand Backup

Before waiting for the scheduled backup, an on-demand backup can be created to test the configuration.

Navigate to:

- AWS Backup
- Protected resources
- Create on-demand backup

Select:

Resource type:
EC2

Select:

Instance:
i-04cfe745666331faf

Backup window:

Create backup now

Backup vault:

EC2-Primary-Backup-Vault

IAM role:

Default role

Retention:

7 days

Start the backup.

---

# Step 10 - Monitor Backup Job

Navigate to:

- AWS Backup
- Jobs
- Backup jobs

A successful backup job should show:

Status: Completed
Message category: Success
Resource type: EC2

Example successful backup jobs from the implementation:

7daccad1-71ce-4ab1-b07d-426982487027

and:

425497c2-2e22-402c-91db-ebe860027ef8

Both completed successfully.

Backup Job Result

Example:

Status:
Completed

Resource:
instance/i-04cfe745666331faf

Resource Type:
EC2

Backup Vault:
EC2-Primary-Backup-Vault

Retention:
7 days

<img width="1920" height="1080" alt="Screenshot 2026-08-12 175509" src="https://github.com/user-attachments/assets/d202e6bb-14e4-4cf6-936e-c2b9d711cebf" />

---

# Step 11 - Verify Primary Recovery Point

Navigate to:

- AWS Backup
- Backup vaults
- EC2-Primary-Backup-Vault
- Recovery points

The recovery point should appear.

Example recovery point:

image/ami-00222ba82661e72f7

Status:

Completed

Resource:

instance/i-04cfe745666331faf

Backup type:

Image

Retention:

7 days

Another recovery point generated during testing was:

image/ami-04cb5c08e2b115b12

with status:

Completed

---

# Step 12 - Verify Cross-Region Copy

Now switch to:

US West (N. California)
us-west-1

Open:

- AWS Backup
- Jobs
- Copy jobs

The cross-region copy should eventually appear.

The expected configuration is:

Source:
us-east-1

Destination:
us-west-1

Destination Vault:
EC2-DR-Backup-Vault

### Copy Flow

- EC2 Instance
- Primary Backup
- Primary Recovery Point
- Cross-Region Copy
- DR Recovery Point

<img width="1920" height="1080" alt="Screenshot 2026-08-12 183858" src="https://github.com/user-attachments/assets/14583452-a8d0-47ef-bec7-cdc632af36ff" />

---

# Step 13 - Verify DR Recovery Point

Switch to:

US West (N. California)
us-west-1

Open:

- AWS Backup
- Backup vaults
- EC2-DR-Backup-Vault

Open:

Recovery points

A successful copied recovery point should appear.

Example recovery point:

image/ami-06e2ca6440b6a7f4a

Status:

Completed

Resource:

instance/i-04cfe745666331faf

Backup type:

Image

This confirms that the EC2 backup was successfully replicated from:

us-east-1

to:

us-west-1

<img width="1920" height="1080" alt="Screenshot 2026-08-12 184058" src="https://github.com/user-attachments/assets/29c40d02-9807-4ba7-aed8-a6a2667813d6" />


---

# Step 14 - Verify Scheduled Backup

The backup plan was configured for daily execution.

The schedule is:

Daily
05:40 PM
Asia/Calcutta (UTC+05:30)

The AWS Backup schedule preview can show upcoming executions such as:

Aug 13, 2026 at 17:40
Aug 14, 2026 at 17:40
Aug 15, 2026 at 17:40
Aug 16, 2026 at 17:40
...

Each scheduled execution contains:

Snapshots
Copy

This indicates that the schedule includes:

EC2 backup
Cross-region copy

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/d30b2eb6-1f61-45b5-b147-a09e3a73e9ae" />

---

# Results

The project successfully demonstrated cross-region EC2 backup replication using AWS Backup. The EC2 instance i-04cfe745666331faf was successfully backed up to the EC2-Primary-Backup-Vault in the us-east-1 (US East – N. Virginia) region. The backup job completed successfully with a Completed status, a Success message, an EC2 resource type, and a 7-day retention period. A primary recovery point, image/ami-00222ba82661e72f7, was also successfully generated.

The recovery point was then successfully copied from us-east-1 (US East – N. Virginia) to us-west-1 (US West – N. California), demonstrating successful cross-region replication. The replicated recovery point, image/ami-06e2ca6440b6a7f4a, was successfully stored in the EC2-DR-Backup-Vault with a Completed status. These results confirm that the implemented AWS Backup solution successfully provides cross-region disaster recovery for the EC2 workload, ensuring that a recovery point is available in the secondary region if the primary AWS Region becomes unavailable.

---

# Conclusion :

This project successfully demonstrates the implementation of a reliable cross-region backup and disaster recovery solution for an Amazon EC2 instance using AWS Backup. The solution was designed to protect an EC2 workload running in the US East (N. Virginia) region (us-east-1) by automatically creating scheduled backups and maintaining a separate copy in the US West (N. California) region (us-west-1).

The implementation started with a Linux-based t3.micro EC2 instance in the primary region. AWS Backup was then configured with a dedicated backup plan, daily backup rule, primary backup vault, and appropriate IAM permissions. The EC2 instance was assigned to the backup plan so that AWS Backup could automatically create recovery points without requiring manual backup operations.

The primary backup was successfully created in the EC2-Primary-Backup-Vault. The completed backup jobs confirmed that the EC2 instance was successfully protected and that recovery points were generated. The backup was stored as an EC2 image based on the underlying EBS volumes.

To provide disaster recovery capability, cross-region copy was configured from us-east-1 to us-west-1. The recovery point was successfully replicated to the EC2-DR-Backup-Vault in the secondary region. The DR vault showed a completed recovery point, confirming that the cross-region replication process was working correctly.

The project also demonstrated important AWS Backup operations such as backup scheduling, retention configuration, backup vault management, recovery-point monitoring, backup-job verification, IAM role usage, and cross-region backup copying. 
Overall, the project provides practical experience with AWS Backup and demonstrates how cross-region replication can be used to improve the availability and recoverability of EC2 workloads. It also provides a strong foundation for implementing more advanced disaster recovery strategies involving multiple workloads, longer retention periods, monitoring, alerting, automated recovery, and infrastructure automation

