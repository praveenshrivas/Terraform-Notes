# Module 05 - Terraform State Management (Part 04)

> **Topics Covered**
>
> * Import Existing Infrastructure
> * Import Blocks (Terraform 1.5+)
> * Terraform Troubleshooting
> * Debug Logging
> * `terraform show`
> * `terraform graph`
> * Common State Errors
> * Production Best Practices
> * Exam Cheat Sheet
> * Interview Questions

---

# Table of Contents

* Why Import Existing Infrastructure?
* terraform import
* Import Workflow
* Import Blocks
* terraform show
* terraform graph
* Debug Logging
* Common Errors
* State Recovery
* Production Best Practices
* Certification Cheat Sheet
* Interview Questions

---

# 1. Why Import Existing Infrastructure?

Terraform is often introduced into environments where infrastructure already exists.

For example:

* Existing EC2 Instances
* Existing VPCs
* Existing S3 Buckets
* Existing IAM Roles

Terraform has no knowledge of these resources unless they are imported.

---

## Example

Imagine your AWS account already contains:

```text
AWS

├── EC2
├── VPC
├── S3 Bucket
└── RDS
```

Terraform starts with an empty state:

```text
terraform.tfstate

(No Resources)
```

If you write:

```hcl
resource "aws_instance" "web" {

}
```

Terraform thinks:

```text
EC2 doesn't exist

↓

Create New EC2
```

But the EC2 already exists.

Instead, we must import it.

---

# 2. What is terraform import?

The `terraform import` command brings an existing cloud resource under Terraform management.

**It does NOT create the resource.**

**It does NOT modify the resource.**

It only updates the Terraform state.

---

## Workflow

```text
Existing AWS Resource

↓

terraform import

↓

Terraform State Updated

↓

Terraform Manages Resource
```

---

## Syntax

```bash
terraform import RESOURCE_ADDRESS RESOURCE_ID
```

---

## Example

Suppose:

EC2 Instance ID

```text
i-0abc123456789
```

Configuration:

```hcl
resource "aws_instance" "web" {

}
```

Import:

```bash
terraform import aws_instance.web i-0abc123456789
```

Terraform now associates:

```text
aws_instance.web

↓

i-0abc123456789
```

---

# 3. Import Workflow

Importing resources follows four steps.

---

## Step 1

Write Terraform Configuration.

Example:

```hcl
resource "aws_instance" "web" {

}
```

---

## Step 2

Initialize Terraform.

```bash
terraform init
```

---

## Step 3

Import Resource.

```bash
terraform import aws_instance.web i-0abc123456789
```

---

## Step 4

Verify Import.

```bash
terraform state show aws_instance.web
```

---

## Diagram

```text
Write Configuration

↓

terraform init

↓

terraform import

↓

terraform plan

↓

No Changes
```

---

# 4. Why Configuration is Required Before Import

A common misconception is:

> "Terraform import creates Terraform code."

It does not.

Terraform only updates the state.

You must still write the configuration.

---

## Incorrect Workflow

```text
terraform import

↓

Configuration Created
```

❌ Wrong

---

## Correct Workflow

```text
Write Configuration

↓

terraform import

↓

State Updated
```

✔ Correct

---

# 5. Import Blocks (Terraform 1.5+)

Terraform 1.5 introduced **Import Blocks**.

Instead of using the CLI command, you can declare imports inside Terraform configuration.

Example:

```hcl
import {

  id = "i-0abc123456789"

  to = aws_instance.web

}
```

Now run:

```bash
terraform plan
```

Terraform performs the import automatically.

---

## Benefits

* Version Controlled
* Repeatable
* Team Friendly
* Easier Automation

---

## Certification Tip

Terraform Associate 004 may include questions on Import Blocks.

Remember:

Classic Method

```bash
terraform import
```

Modern Method

```hcl
import {

}
```

---

# 6. terraform show

## Purpose

Displays the current Terraform state in a human-readable format.

---

## Syntax

```bash
terraform show
```

---

## Output

```text
aws_instance.web

AMI

Instance Type

Public IP

Private IP

Tags
```

---

## Uses

* View State
* Verify Infrastructure
* Debug Resources
* Check Outputs

---

## Show Plan

Terraform can also display saved plans.

```bash
terraform show tfplan
```

Useful for CI/CD review.

---

# 7. terraform graph

## Purpose

Visualizes Terraform resource dependencies.

---

## Syntax

```bash
terraform graph
```

Output:

```text
VPC

↓

Subnet

↓

EC2

↓

Security Group
```

---

## Why Useful?

Terraform determines execution order based on dependencies.

Example:

```text
VPC

↓

Subnet

↓

EC2
```

Terraform creates:

1. VPC
2. Subnet
3. EC2

Automatically.

---

# 8. Debug Logging

Terraform provides detailed logs for troubleshooting.

Environment Variable:

```bash
export TF_LOG=TRACE
```

Windows:

```powershell
$env:TF_LOG="TRACE"
```

---

## Log Levels

| Level | Description         |
| ----- | ------------------- |
| TRACE | Everything          |
| DEBUG | Debug Messages      |
| INFO  | General Information |
| WARN  | Warnings            |
| ERROR | Errors              |

---

## Example

```bash
export TF_LOG=DEBUG

terraform apply
```

---

# 9. Save Logs

Instead of displaying logs on the screen:

```bash
export TF_LOG_PATH=terraform.log
```

Terraform writes logs into:

```text
terraform.log
```

Useful for troubleshooting production issues.

---

# 10. Common Terraform Errors

---

## Backend Initialization Error

Example:

```text
Backend initialization required
```

Solution:

```bash
terraform init
```

---

## State Lock Error

Example:

```text
Error acquiring state lock
```

Possible Reasons:

* Another user is running Terraform.
* Previous execution crashed.

Solutions:

```bash
terraform apply -lock-timeout=300s
```

or

```bash
terraform force-unlock
```

---

## Provider Version Mismatch

Example:

```text
Provider version not compatible
```

Solution:

```bash
terraform init -upgrade
```

---

## Resource Already Exists

Example:

```text
Bucket already exists
```

Solution:

Import the resource.

```bash
terraform import
```

---

## State Drift

Terraform Plan:

```text
~ Update Required
```

Someone changed infrastructure manually.

Solution:

* Review Changes
* Decide whether to keep or revert
* Apply if appropriate

---

# 11. Recovering Terraform State

Sometimes:

* State Deleted
* Laptop Lost
* Corrupted State

Recovery options:

## Local Backup

```text
terraform.tfstate.backup
```

---

## S3 Versioning

Restore previous object version.

---

## HCP Terraform

Restore previous state version.

---

## Disaster Recovery Workflow

```text
State Corrupted

↓

Restore Backup

↓

terraform init

↓

terraform plan

↓

Verify

↓

terraform apply
```

---

# 12. Production Best Practices

## Never Store State in Git

Add:

```gitignore
*.tfstate
*.tfstate.*

.terraform/
```

---

## Enable Bucket Versioning

Protects against accidental deletion.

---

## Enable Encryption

Encrypt state at rest.

---

## Enable State Locking

Always configure DynamoDB with S3 backend.

---

## Use Least Privilege

Restrict access to:

* S3 Bucket
* DynamoDB
* HCP Terraform

---

## Separate State Files

Recommended:

```text
network/

compute/

database/

security/
```

Avoid storing everything in one state file.

---

## Protect Sensitive Variables

Use:

* AWS Secrets Manager
* HashiCorp Vault
* HCP Variable Sets

Avoid hardcoding secrets.

---

# 13. End-to-End Terraform State Lifecycle

```text
Write Configuration

↓

terraform init

↓

terraform plan

↓

terraform apply

↓

State Created

↓

Modify Configuration

↓

terraform plan

↓

terraform apply

↓

State Updated

↓

Store in Remote Backend

↓

Enable Locking

↓

Collaborate Safely
```

---

# 14. Certification Cheat Sheet

| Topic                | Remember                                 |
| -------------------- | ---------------------------------------- |
| terraform.tfstate    | Default State File                       |
| Local Backend        | Default Backend                          |
| Remote Backend       | Recommended for Production               |
| S3 Backend           | Most Common AWS Backend                  |
| DynamoDB             | State Locking                            |
| terraform state list | List Managed Resources                   |
| terraform state show | View Resource Details                    |
| terraform state mv   | Move Resource in State                   |
| terraform state rm   | Remove Resource from State               |
| terraform import     | Import Existing Resource                 |
| Import Block         | Terraform 1.5+ Import Method             |
| terraform show       | Display Current State                    |
| terraform graph      | Display Resource Dependency Graph        |
| TF_LOG               | Enable Debug Logging                     |
| TF_LOG_PATH          | Save Logs to File                        |
| Refresh-only         | Update State Only                        |
| Drift                | Infrastructure Changed Outside Terraform |

---

# 15. Frequently Asked Exam Questions

### Q1. Does `terraform import` create resources?

❌ No

It only updates Terraform State.

---

### Q2. Does `terraform state rm` delete infrastructure?

❌ No

It removes the resource from Terraform State only.

---

### Q3. Which backend supports state locking in AWS?

✅ Amazon S3 + DynamoDB

---

### Q4. Which command visualizes dependencies?

✅ `terraform graph`

---

### Q5. Which command displays the current state?

✅ `terraform show`

---

### Q6. Can backend configuration use variables?

❌ No

---

### Q7. Does Refresh-only mode change infrastructure?

❌ No

It updates only the state.

---

### Q8. What causes Drift?

Infrastructure changes made outside Terraform.

---

# 16. Interview Questions

### Beginner

1. What is Terraform State?
2. Why do we need a state file?
3. What is a Backend?
4. Difference between Local and Remote Backend?
5. Why use DynamoDB with S3?

---

### Intermediate

6. Explain the Terraform import process.
7. What happens if the state file is deleted?
8. How does Terraform detect Drift?
9. Explain State Locking.
10. How do you migrate local state to S3?

---

### Advanced

11. Explain the complete Terraform State lifecycle.
12. How would you recover from a corrupted state file?
13. How do you safely rename resources?
14. How would you split a large Terraform state?
15. What are the risks of manually editing `terraform.tfstate`?

---

# Module 05 Final Summary

Congratulations! You have completed one of the most important modules in Terraform.

By completing this module, you should now understand:

* ✅ Terraform State fundamentals
* ✅ State file structure
* ✅ State lifecycle
* ✅ State commands
* ✅ Resource Drift
* ✅ Refresh-only mode
* ✅ State refactoring
* ✅ Local & Remote Backends
* ✅ Amazon S3 Backend
* ✅ DynamoDB State Locking
* ✅ Backend migration
* ✅ Import existing infrastructure
* ✅ Import Blocks
* ✅ Troubleshooting Terraform
* ✅ Debug logging
* ✅ Production best practices

---

# Final Revision Checklist

Before attempting the Terraform Associate exam, ensure you can confidently answer these questions:

* [ ] What is Terraform State?
* [ ] Why does Terraform need a state file?
* [ ] What information is stored in `terraform.tfstate`?
* [ ] Difference between Local and Remote Backends?
* [ ] How does State Locking work?
* [ ] Why is DynamoDB used with S3?
* [ ] Difference between `terraform state rm` and `terraform destroy`?
* [ ] What is Resource Drift?
* [ ] How do you detect and fix Drift?
* [ ] How do you import existing infrastructure?
* [ ] What is an Import Block?
* [ ] What does `terraform show` do?
* [ ] What does `terraform graph` do?
* [ ] How do you enable Terraform debug logs?
* [ ] What are the best practices for managing Terraform state?

---

## 🎯 What's Next?

You have now completed **Module 05 – State Management & Infrastructure Maintenance**, which is one of the highest-weighted topics in the Terraform Associate certification.

The next recommended module in your learning path is:

> **Module 06 – HCP Terraform (Terraform Cloud)**

You'll build on the concepts learned here by exploring:

* Workspaces
* Projects
* Variable Sets
* Remote Operations
* CLI-driven & VCS-driven workflows
* Policy Management (Sentinel)
* Drift Detection in HCP Terraform
* Teams & Permissions
* Dynamic Credentials
* Health Assessments

Mastering Modules **05 and 06** together gives you a strong foundation for both the certification exam and real-world Terraform deployments.
