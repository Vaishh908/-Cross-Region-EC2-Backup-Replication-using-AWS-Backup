# Cross-Region EC2 Backup Replication using AWS Backup

---

##  Project Overview

Cross-Region EC2 Backup Replication using AWS Backup is a disaster recovery solution designed to protect an Amazon EC2 instance by automatically creating scheduled backups and replicating them across AWS regions. In this project, a Linux-based t3.micro EC2 instance is deployed in the US East (N. Virginia) region (us-east-1), which acts as the primary region. AWS Backup is configured with a backup plan named EC2-Cross-Region-Backup-Plan and a daily backup rule named Daily-EC2-Backup. The backups are stored in the EC2-Primary-Backup-Vault with a 7-day retention period. AWS Backup then automatically copies the recovery point to the EC2-DR-Backup-Vault in the US West (N. California) region (us-west-1). The successful completion of backup jobs and the presence of recovery points in both regions verify that cross-region backup replication is working correctly. This architecture provides an additional layer of protection against EC2 instance failure, data loss, or a disruption in the primary AWS region, while allowing the replicated recovery point in the secondary region to be used for disaster recovery and restoration.

---

## Architecture Diagram

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

# AWS Region Configuration

The project uses two AWS regions.

| Component         | Primary Region        | DR Region               |
| ----------------- | --------------------- | ----------------------- |
| Region Name       | US East (N. Virginia) | US West (N. California) |
| Region Code       | `us-east-1`           | `us-west-1`             |
| Purpose           | Primary backup        | Disaster recovery       |
| EC2               | Yes                   | Restore target          |
| Backup Vault      | Yes                   | Yes                     |
| Recovery Point    | Yes                   | Yes                     |
| Cross-Region Copy | Source                | Destination             |

---

# EC2 Configuration

The source EC2 instance used in this project:

| Configuration    | Value                 |
| ---------------- | --------------------- |
| Operating System | Linux                 |
| Instance Type    | `t3.micro`            |
| Region           | `us-east-1`           |
| Instance ID      | `i-04cfe745666331faf` |
| Purpose          | Source EC2            |
| Backup Method    | AWS Backup            |

---

# Implementation Steps

# Step 1 - Create EC2 Instance

Login to the AWS Management Console.

Search for:

EC2

Open the EC2 service.

Select:

Launch Instance

Configure the instance.

| Setting       | Value             |
| ------------- | ----------------- |
| Name          | EC2-Backup-Source |
| AMI           | Ubuntu/Linux      |
| Instance Type | `t3.micro`        |
| Key Pair      | Project.pem       |
| Region        | `us-east-1`       |
| Storage       | Default EBS       |

Launch the instance.

---

# Step 2 - Verify EC2 Instance

After launching the EC2 instance, verify that the instance is running.

The instance used in this project:

Instance ID:
i-04cfe745666331faf

Instance type:

t3.micro

Operating system:

Linux

Region:

us-east-1

---

# Step 3 - Open AWS Backup

From the AWS Management Console, search:

AWS Backup

Make sure the selected region is:

US East (N. Virginia)
us-east-1

AWS Backup is used to automate the backup and cross-region replication process.

---

# Step 4 - Create Primary Backup Vault

Navigate to:

AWS Backup
    ↓
Backup vaults
    ↓
Create backup vault

Create the following vault:

EC2-Primary-Backup-Vault

Region:

us-east-1

The vault is used to store the EC2 backup recovery points in the primary region.

Primary Vault

| Configuration | Value                      |
| ------------- | -------------------------- |
| Vault Name    | `EC2-Primary-Backup-Vault` |
| Region        | `us-east-1`                |
| Purpose       | Primary backup storage     |
| Resource      | EC2                        |

---

# Step 5 - Create DR Backup Vault

Change the AWS region to:

US West (N. California)
us-west-1

Navigate to:

AWS Backup
    ↓
Backup vaults
    ↓
Create backup vault

Create:

EC2-DR-Backup-Vault
DR Vault
| Configuration | Value                 |
| ------------- | --------------------- |
| Vault Name    | `EC2-DR-Backup-Vault` |
| Region        | `us-west-1`           |
| Purpose       | Disaster recovery     |
| Backup Source | `us-east-1`           |

The DR vault is the destination for cross-region backup copies.

---

# Step 6 - Create Backup Plan

Return to:

US East (N. Virginia)
us-east-1

Open:

AWS Backup
    ↓
Backup plans
    ↓
Create backup plan

Select:

Build a new plan

Use the following configuration.

Backup Plan
EC2-Cross-Region-Backup-Plan

---

# Step 7 - Configure Backup Rule

Create a backup rule:

Daily-EC2-Backup
Backup Rule Configuration

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

---

# Step 8 - Configure Cross-Region Copy

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

Copy Configuration

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

---

# Step 9 - Assign EC2 Resource

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

EC2-Backup-Assignment
        │
        ▼
EC2
        │
        ▼
i-04cfe745666331faf

---

# Step 10 - Verify Backup Plan

After creating the backup plan, the plan should show:

EC2-Cross-Region-Backup-Plan

The backup rule should show:

Daily-EC2-Backup

The backup vault should show:

EC2-Primary-Backup-Vault

The destination should show:

EC2-DR-Backup-Vault

Final Backup Plan Configuration :

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

---

# Step 11 - Create On-Demand Backup

Before waiting for the scheduled backup, an on-demand backup can be created to test the configuration.

Navigate to:

AWS Backup
    ↓
Protected resources
    ↓
Create on-demand backup

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

# Step 12 - Monitor Backup Job

Navigate to:

AWS Backup
    ↓
Jobs
    ↓
Backup jobs

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

---

# Step 13 - Verify Primary Recovery Point

Navigate to:

AWS Backup
    ↓
Backup vaults
    ↓
EC2-Primary-Backup-Vault
    ↓
Recovery points

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

# Step 14 - Verify Cross-Region Copy

Now switch to:

US West (N. California)
us-west-1

Open:

AWS Backup
    ↓
Jobs
    ↓
Copy jobs

The cross-region copy should eventually appear.

The expected configuration is:

Source:
us-east-1

Destination:
us-west-1

Destination Vault:
EC2-DR-Backup-Vault
Copy Flow
EC2 Instance
     │
     ▼
Primary Backup
     │
     ▼
Primary Recovery Point
     │
     ▼
Cross-Region Copy
     │
     ▼
DR Recovery Point
Step 15 - Verify DR Recovery Point

Switch to:

US West (N. California)
us-west-1

Open:

AWS Backup
    ↓
Backup vaults
    ↓
EC2-DR-Backup-Vault

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
Step 16 - Verify Scheduled Backup

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

---

# Step 17 - Disaster Recovery

If the primary EC2 instance becomes unavailable, the recovery point stored in:

us-west-1

can be used for disaster recovery.

Navigate to:

AWS Backup
    ↓
Backup vaults
    ↓
EC2-DR-Backup-Vault
    ↓
Recovery points

Select the completed recovery point.

Choose:

Restore

AWS Backup can then be used to restore the EC2 workload in the DR region.

Conceptually:

Primary EC2
    │
    X
Failure
    │
    ▼
DR Recovery Point
    │
    ▼
Restore
    │
    ▼
EC2 in us-west-1

# Verification Checklist

The following checklist was used to verify the project.

| Verification                   | Status |
| ------------------------------ | ------ |
| EC2 instance created           | ✅      |
| Linux OS                       | ✅      |
| t3.micro instance              | ✅      |
| Primary region configured      | ✅      |
| DR region configured           | ✅      |
| Primary Backup Vault created   | ✅      |
| DR Backup Vault created        | ✅      |
| Backup Plan created            | ✅      |
| Daily Backup Rule created      | ✅      |
| EC2 resource assigned          | ✅      |
| IAM Default Backup Role        | ✅      |
| On-demand backup tested        | ✅      |
| Backup job completed           | ✅      |
| Primary recovery point created | ✅      |
| Cross-region copy configured   | ✅      |
| DR recovery point created      | ✅      |
| 7-day retention configured     | ✅      |

Results

The project successfully demonstrated cross-region EC2 backup replication.

Primary Backup

The EC2 instance:

i-04cfe745666331faf

was successfully backed up to:

EC2-Primary-Backup-Vault

in:

us-east-1
Backup Job

The backup jobs completed successfully.

Example:

Status: Completed
Message: Success
Resource Type: EC2
Retention: 7 days
Primary Recovery Point

A completed recovery point was generated.

Example:

image/ami-00222ba82661e72f7

Status:

Completed
Cross-Region Replication

The backup was copied from:

US East (N. Virginia)
us-east-1

to:

US West (N. California)
us-west-1
DR Recovery Point

A recovery point was successfully available in:

EC2-DR-Backup-Vault

Example:

image/ami-06e2ca6440b6a7f4a

Status:

Completed

---

# Troubleshooting

### Problem 1 - Recovery Point Not Showing

If the recovery point is not immediately visible, check:

AWS Backup
→ Jobs
→ Backup Jobs

Verify that the job status is:

Completed

Do not check the recovery vault immediately while the backup job is still running.

Refresh the page after the backup job completes.

### Problem 2 - Copy Job Not Showing

Cross-region copy can take additional time after the primary backup completes.

Check:

AWS Backup
→ Jobs
→ Copy Jobs

Also verify:

Destination Region:
us-west-1

Destination Vault:
EC2-DR-Backup-Vault

### Problem 3 - Duplicate CopyActions

Error:

Duplicate CopyActions provided in backupRule.

Solution:

Open the backup rule and make sure there is only one destination:

us-west-1
EC2-DR-Backup-Vault

Remove duplicate copy actions and save the rule.

### Problem 4 - Scheduled Backup Shows Future Date

AWS Backup schedules are based on the configured schedule and current time.

For example:

Daily
05:40 PM
Asia/Calcutta

The console may show the next execution as:

Aug 13, 2026 at 17:40

This is normal if today's scheduled execution time has already passed.

# Backup Retention

The project uses:

7 days

for the primary backup.

The cross-region copied recovery point also uses:

7 days

Therefore, recovery points older than the configured retention period are automatically expired according to AWS Backup lifecycle management.

# Conclusion :

This project successfully demonstrates the implementation of a reliable cross-region backup and disaster recovery solution for an Amazon EC2 instance using AWS Backup. The solution was designed to protect an EC2 workload running in the US East (N. Virginia) region (us-east-1) by automatically creating scheduled backups and maintaining a separate copy in the US West (N. California) region (us-west-1).

The implementation started with a Linux-based t3.micro EC2 instance in the primary region. AWS Backup was then configured with a dedicated backup plan, daily backup rule, primary backup vault, and appropriate IAM permissions. The EC2 instance was assigned to the backup plan so that AWS Backup could automatically create recovery points without requiring manual backup operations.

The primary backup was successfully created in the EC2-Primary-Backup-Vault. The completed backup jobs confirmed that the EC2 instance was successfully protected and that recovery points were generated. The backup was stored as an EC2 image based on the underlying EBS volumes.

To provide disaster recovery capability, cross-region copy was configured from us-east-1 to us-west-1. The recovery point was successfully replicated to the EC2-DR-Backup-Vault in the secondary region. The DR vault showed a completed recovery point, confirming that the cross-region replication process was working correctly.

The project also demonstrated important AWS Backup operations such as backup scheduling, retention configuration, backup vault management, recovery-point monitoring, backup-job verification, IAM role usage, and cross-region backup copying. 
Overall, the project provides practical experience with AWS Backup and demonstrates how cross-region replication can be used to improve the availability and recoverability of EC2 workloads. It also provides a strong foundation for implementing more advanced disaster recovery strategies involving multiple workloads, longer retention periods, monitoring, alerting, automated recovery, and infrastructure automation

