# Module 05 - Terraform State Management (Part 02)

> **Topics Covered**
>
> * Terraform State Commands
> * Resource Drift
> * Refresh-only Mode
> * State Refactoring
> * The `moved` Block
> * Common State Management Scenarios
> * Certification Tips
> * Hands-on Labs

---

# Table of Contents

* Terraform State Commands
* Inspecting the State
* Modifying the State
* Resource Drift
* Refresh-only Mode
* State Refactoring
* The `moved` Block
* Hands-on Labs
* Exam Tips
* Interview Questions

---

# 1. Terraform State Commands

Terraform provides a set of commands to inspect and manage the state file without manually editing it.

> **Golden Rule:** Never edit `terraform.tfstate` manually. Use Terraform state commands whenever possible.

---

# State Command Overview

| Command                | Purpose                                  |
| ---------------------- | ---------------------------------------- |
| `terraform state list` | List all resources in the state          |
| `terraform state show` | Display details of a specific resource   |
| `terraform state mv`   | Move or rename a resource in the state   |
| `terraform state rm`   | Remove a resource from the state         |
| `terraform state pull` | Download remote state locally            |
| `terraform state push` | Upload a local state file to the backend |

---

# 2. terraform state list

## Purpose

Displays every resource currently managed by Terraform.

## Syntax

```bash
terraform state list
```

---

## Example

Suppose your infrastructure contains:

* EC2 Instance
* S3 Bucket
* VPC
* Security Group

Running:

```bash
terraform state list
```

Returns:

```text
aws_instance.web

aws_security_group.web_sg

aws_s3_bucket.logs

aws_vpc.main
```

---

## Why Use It?

Useful when:

* You forgot the resource name.
* You want to inspect resources.
* Preparing for imports.
* Troubleshooting state issues.

---

## Certification Tip

`terraform state list`

✔ Lists resources from the **state file**

❌ Does **not** query AWS.

Many candidates assume it checks the cloud provider. It only reads the current state.

---

# 3. terraform state show

## Purpose

Displays all stored attributes of a resource.

## Syntax

```bash
terraform state show RESOURCE_NAME
```

---

## Example

```bash
terraform state show aws_instance.web
```

Output:

```text
id = i-0123456789abcdef

ami = ami-0abcd12345

instance_type = t3.micro

private_ip = 10.0.1.15

public_ip = 13.233.xx.xx
```

---

## When Should You Use It?

* View resource attributes
* Debug issues
* Verify imported resources
* Check computed values

---

## Difference Between list and show

| Command    | Purpose                |
| ---------- | ---------------------- |
| state list | Lists resource names   |
| state show | Shows resource details |

---

# 4. terraform state mv

## Purpose

Moves a resource from one address to another inside the state.

It **does not recreate** the infrastructure.

---

## Why Use It?

Suppose you rename:

Before:

```hcl
resource "aws_instance" "web" {

}
```

After:

```hcl
resource "aws_instance" "frontend" {

}
```

Without updating the state:

Terraform thinks:

```text
Delete web

Create frontend
```

Instead, move the state:

```bash
terraform state mv aws_instance.web aws_instance.frontend
```

Terraform now understands:

```text
Same Resource

New Name
```

---

## Benefits

* No downtime
* No recreation
* Preserves resource IDs

---

## Exam Tip

`terraform state mv`

✔ Changes resource address

❌ Does not recreate infrastructure

---

# 5. terraform state rm

## Purpose

Removes a resource from the Terraform state **without deleting the actual resource**.

---

## Example

```bash
terraform state rm aws_instance.web
```

Terraform forgets about the EC2 instance.

The EC2 instance still exists in AWS.

---

## Result

Before:

```text
Terraform

↓

EC2
```

After:

```text
Terraform

(No Knowledge)

EC2 Still Exists
```

---

## Use Cases

* Stop managing a resource
* Remove broken resources
* Prepare for import
* Migration

---

## Important

`terraform state rm`

≠

`terraform destroy`

Destroy deletes infrastructure.

State rm only removes it from state.

---

# 6. terraform state pull

## Purpose

Downloads the latest remote state.

Example:

```bash
terraform state pull
```

Useful when:

* Using Remote Backend
* Inspecting state
* Backup

---

# 7. terraform state push

## Purpose

Uploads a local state file.

Example:

```bash
terraform state push terraform.tfstate
```

---

## Warning

Use only when necessary.

Uploading an incorrect state file can corrupt infrastructure management.

---

# 8. Resource Drift

## What is Resource Drift?

Resource Drift occurs when infrastructure changes **outside Terraform**.

Terraform configuration:

```text
EC2

t3.micro
```

Someone changes manually:

```text
AWS Console

↓

t3.large
```

Now:

```text
Terraform State

↓

t3.micro

Actual Infrastructure

↓

t3.large
```

Infrastructure and state no longer match.

This is called **Drift**.

---

# Why Drift Happens

Most common reasons:

* Manual AWS Console changes
* CLI modifications
* CloudFormation changes
* Scripts
* Auto Scaling
* Other Terraform projects

---

# Problems Caused by Drift

* Incorrect Plans
* Unexpected Changes
* Resource Recreation
* Configuration Mismatch

---

# Detecting Drift

Terraform compares:

```text
Configuration

↓

State

↓

Actual Infrastructure
```

If different:

Terraform reports changes during:

```bash
terraform plan
```

---

# Example

Current State

```text
instance_type

t3.micro
```

AWS

```text
instance_type

t3.large
```

Terraform Plan

```text
~ instance_type

t3.large

↓

t3.micro
```

Terraform attempts to restore the desired state.

---

# 9. Refresh-only Mode

Terraform provides Refresh-only mode to synchronize the state without modifying infrastructure.

Command:

```bash
terraform apply -refresh-only
```

---

## What Happens?

Terraform:

✔ Reads cloud infrastructure

✔ Updates the state file

✔ Makes no infrastructure changes

---

## Workflow

```text
AWS

↓

Read Infrastructure

↓

Update State

↓

Finish
```

---

## Difference

Normal Apply

```text
State

↓

Infrastructure Updated
```

Refresh-only

```text
Infrastructure

↓

State Updated
```

---

## Certification Tip

Refresh-only mode updates the **state**, not the infrastructure.

---

# 10. State Refactoring

## What is Refactoring?

Changing Terraform code structure without recreating infrastructure.

Examples:

* Rename resources
* Move resources into modules
* Rename modules
* Reorganize directories

---

# Problem

Before:

```hcl
resource "aws_s3_bucket" "logs" {

}
```

After:

```hcl
resource "aws_s3_bucket" "application_logs" {

}
```

Terraform thinks:

```text
Delete logs

Create application_logs
```

Not what we want.

---

# Solution

Use:

```bash
terraform state mv
```

Or

Terraform 1.5+

Use:

```hcl
moved {

}
```

---

# 11. moved Block

Terraform introduced the `moved` block to simplify state refactoring.

Example:

```hcl
moved {

from = aws_instance.web

to = aws_instance.frontend

}
```

During:

```bash
terraform plan
```

Terraform understands:

```text
Same Resource

New Address
```

No recreation occurs.

---

## Benefits

* Safer refactoring
* Version controlled
* Easy to review
* Team friendly

---

## Certification Tip

Prefer the `moved` block over manual state manipulation when refactoring Terraform configuration.

---

# 12. Common Refactoring Scenarios

### Rename Resource

```text
web

↓

frontend
```

---

### Move to Module

Before

```text
aws_instance.web
```

After

```text
module.compute.aws_instance.web
```

---

### Rename Module

Before

```text
module.dev
```

After

```text
module.production
```

---

# Hands-on Lab 1

List Resources

```bash
terraform state list
```

Observe:

* EC2
* VPC
* S3
* Security Group

---

# Hands-on Lab 2

Inspect Resource

```bash
terraform state show aws_instance.web
```

Observe:

* Instance ID
* Public IP
* Private IP
* Tags

---

# Hands-on Lab 3

Rename Resource

Old:

```hcl
aws_instance.web
```

New:

```hcl
aws_instance.frontend
```

Move state:

```bash
terraform state mv aws_instance.web aws_instance.frontend
```

Run:

```bash
terraform plan
```

No resources should be recreated.

---

# Hands-on Lab 4

Remove Resource from State

```bash
terraform state rm aws_instance.frontend
```

Verify:

```bash
terraform state list
```

The EC2 instance disappears from Terraform state but still exists in AWS.

---

# Hands-on Lab 5

Detect Drift

1. Create an EC2 using Terraform.
2. Open AWS Console.
3. Change the instance type manually.
4. Run:

```bash
terraform plan
```

Observe Terraform detecting the drift.

---

# Certification Tips

✅ `terraform state list` lists resources from the state file.

✅ `terraform state show` displays resource attributes.

✅ `terraform state mv` renames or moves resources without recreation.

✅ `terraform state rm` removes a resource from state but does not delete it.

✅ Resource Drift occurs when infrastructure changes outside Terraform.

✅ Refresh-only mode updates the state, not the infrastructure.

✅ The `moved` block is the recommended approach for refactoring in modern Terraform.

---

# Common Interview Questions

### 1. What is Resource Drift?

### 2. How can you detect Drift?

### 3. Difference between `terraform state rm` and `terraform destroy`?

### 4. Difference between `terraform state list` and `terraform state show`?

### 5. What is the purpose of `terraform state mv`?

### 6. When should you use Refresh-only Mode?

### 7. What is the `moved` block?

### 8. How do you rename a Terraform resource without recreating it?

### 9. Why should you avoid manually editing the state file?

### 10. What happens if you remove a resource from the state but not from the cloud?

---

# Module 05 - Part 02 Summary

In this part, you learned:

* All major Terraform State commands
* How to inspect and modify the state safely
* What Resource Drift is and how to detect it
* The purpose of Refresh-only mode
* How to refactor Terraform configuration
* How the `moved` block simplifies resource renaming and migration
* Practical hands-on exercises for common state management tasks

> **Next:** Part 03 – Local & Remote Backends, S3 Backend Configuration, Backend Migration, State Locking with DynamoDB, and Production Best Practices.
