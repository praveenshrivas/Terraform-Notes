
# Module 6: HCP Terraform (Terraform Cloud)

## Exam Weight

**Medium-High (10-15%)**

> HCP Terraform (formerly Terraform Cloud) is HashiCorp's managed SaaS platform for Terraform that provides remote state management, collaboration, governance, and automation capabilities.

---

# 1. Introduction to HCP Terraform

## What is HCP Terraform?

HCP Terraform is HashiCorp's cloud-hosted service for managing Terraform workflows.

Instead of storing Terraform state files locally and running Terraform from individual machines, HCP Terraform provides:

* Remote State Storage
* State Locking
* Team Collaboration
* Remote Execution
* Policy Enforcement
* Secret Management
* Drift Detection

## Benefits

### Remote State Storage

Stores state centrally.

### State Locking

Prevents concurrent modifications.

### Collaboration

Allows multiple engineers to work on the same infrastructure.

### Governance

Provides policy enforcement and approval workflows.

### Security

Stores variables and secrets securely.

### Exam Tip

**What problem does HCP Terraform solve?**

* Centralized State Management
* Collaboration
* Governance
* Automation

---

# 2. Workspaces

## What is a Workspace?

A Workspace is the primary working unit in HCP Terraform.

A workspace contains:

* Terraform Configuration
* State File
* Variables
* Run History

Think of a Workspace as:

```text
Configuration + State + Variables + Execution Environment
```

## Example

```text
workspace-dev
workspace-qa
workspace-prod
```

Each workspace has:

```text
Own State
Own Variables
Own Runs
```

## Benefits

* Environment Isolation
* State Separation
* Better Management

### Exam Tip

Do not confuse:

#### Terraform CLI Workspace

```bash
terraform workspace new dev
```

Used locally.

#### HCP Terraform Workspace

Cloud-managed execution environment.

---

# 3. Projects

## What are Projects?

Projects are containers used to organize multiple workspaces.

Example:

```text
Project
├── Dev Workspace
├── QA Workspace
└── Prod Workspace
```

## Benefits

* Better Organization
* Easier Navigation
* Improved Access Control

### Exam Tip

| Project             | Workspace              |
| ------------------- | ---------------------- |
| Groups Workspaces   | Manages Infrastructure |
| Organizational Unit | Execution Unit         |

---

# 4. Variable Sets

## What are Variable Sets?

Variable Sets allow variables to be shared across multiple workspaces.

Without Variable Sets:

```text
Workspace1
AWS_REGION=us-east-1

Workspace2
AWS_REGION=us-east-1

Workspace3
AWS_REGION=us-east-1
```

With Variable Sets:

```text
Variable Set
AWS_REGION=us-east-1
OWNER=DevOps
```

Attached to:

```text
Workspace1
Workspace2
Workspace3
```

## Benefits

* Reusability
* Consistency
* Centralized Management

### Exam Tip

Variable Sets are used to:

✅ Share variables across multiple workspaces.

---

# 5. CLI-Driven Workflow

## What is CLI-Driven Workflow?

Terraform commands are executed from the local machine but runs occur in HCP Terraform.

## Login

```bash
terraform login
```

Generates an API token.

## Backend Configuration

```hcl
terraform {
  cloud {
    organization = "my-org"

    workspaces {
      name = "dev-workspace"
    }
  }
}
```

## Execute Commands

```bash
terraform init
terraform plan
terraform apply
```

### Exam Tip

```text
User Starts Run Locally
          ↓
HCP Terraform Executes Run
```

---

# 6. VCS-Driven Workflow

## What is VCS-Driven Workflow?

Terraform runs are automatically triggered when code is pushed to a version control system.

Supported VCS:

* GitHub
* GitLab
* Bitbucket

## Workflow

```text
Git Push
   ↓
Webhook Trigger
   ↓
HCP Terraform
   ↓
Plan
   ↓
Apply
```

## Benefits

* Fully Automated
* CI/CD Friendly
* Version Controlled

### Exam Tip

Code push automatically triggers Terraform runs.

---

# 7. Remote Operations

## What are Remote Operations?

Terraform plan and apply operations run on HashiCorp infrastructure instead of local machines.

### Local Execution

```text
Laptop
├── Plan
└── Apply
```

### Remote Execution

```text
Laptop
   ↓
HCP Terraform
   ↓
Plan
   ↓
Apply
```

## Benefits

* Security
* Consistency
* Auditability

---

# 8. State Migration

## Purpose

Move existing local state files into HCP Terraform.

### Before

```text
terraform.tfstate
```

Stored locally.

### After

```text
HCP Terraform
```

Stored remotely.

## Migration

```bash
terraform init
```

Terraform asks:

```text
Copy existing state?
```

Answer:

```text
yes
```

### Exam Tip

State Migration transfers local state to a remote backend.

---

# 9. Run Triggers

## What are Run Triggers?

Run Triggers automatically start runs in dependent workspaces.

### Example

Network Workspace

```text
Creates:
- VPC
- Subnets
```

Application Workspace depends on Network.

```text
Network Apply
      ↓
Run Trigger
      ↓
Application Apply
```

### Exam Tip

Run Triggers automate execution between dependent workspaces.

---

# 10. Remote State Data Source

## Purpose

Allows one workspace to consume outputs from another workspace.

### Workspace A

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

### Workspace B

```hcl
data "terraform_remote_state" "network" {
}
```

Reads:

```text
vpc_id
```

### Exam Tip

| Feature      | Purpose            |
| ------------ | ------------------ |
| Run Trigger  | Triggers Execution |
| Remote State | Shares Outputs     |

---

# 11. Teams & Permissions

## Purpose

Manage user access within HCP Terraform.

### Team Examples

```text
Developers
DevOps
Admins
Auditors
```

### Permission Examples

* Read
* Write
* Plan
* Apply
* Admin

### Principle

Follow Least Privilege Access.

### Exam Tip

Teams simplify permission management across workspaces.

---

# 12. Dynamic Credentials

## Traditional Method

Store cloud credentials as variables.

```text
AWS_ACCESS_KEY
AWS_SECRET_KEY
```

Problems:

* Long-lived Credentials
* Security Risk

## Dynamic Credentials

Temporary credentials generated automatically.

Examples:

* AWS IAM Roles
* Azure Managed Identity
* GCP Service Accounts

## Benefits

* No Static Secrets
* Short-Lived Credentials
* Improved Security

### Exam Tip

Dynamic Credentials eliminate the need for static cloud credentials.

---

# 13. Health Assessments

## Purpose

Provides visibility into workspace health.

Monitors:

* Configuration Quality
* State Health
* Security Risks

## Benefits

Early issue detection.

### Exam Tip

Health Assessments provide operational visibility into Terraform workspaces.

---

# 14. Drift Detection

## What is Drift?

Drift occurs when infrastructure changes outside Terraform.

Example:

Terraform State:

```text
EC2 = t3.medium
```

Manual Change:

```text
EC2 = t3.large
```

Result:

```text
Actual Infrastructure ≠ Terraform State
```

## HCP Terraform

Automatically detects drift.

### Exam Tip

Drift = Infrastructure changed outside Terraform control.

---

# 15. Policy Management

## What is Policy as Code?

Policies automatically enforce organizational rules.

Implemented using:

### Sentinel

HashiCorp's Policy-as-Code Framework.

## Examples

### Approved Regions Only

```text
us-east-1
us-west-2
```

### Mandatory Tags

```text
Owner
Environment
CostCenter
```

### Block Public S3 Buckets

Prevent insecure configurations.

### Exam Tip

Policies are evaluated before Apply operations.

---

# 16. Change Requests

## Purpose

Introduce review and approval processes before infrastructure changes are applied.

## Workflow

```text
Plan
 ↓
Review
 ↓
Approval
 ↓
Apply
```

## Benefits

* Governance
* Controlled Deployments
* Reduced Risk

### Exam Tip

Change Requests provide approval workflows for infrastructure changes.

---

# 17. Explorer

## What is Explorer?

Explorer provides visibility across infrastructure resources and workspaces.

Shows:

* Projects
* Workspaces
* Resources
* Relationships

## Benefits

* Better Visualization
* Infrastructure Discovery
* Dependency Understanding

### Exam Tip

Explorer helps visualize infrastructure managed by HCP Terraform.

---

# Quick Revision Sheet

| Topic               | Key Point                          |
| ------------------- | ---------------------------------- |
| Workspace           | Main unit in HCP Terraform         |
| Project             | Groups multiple workspaces         |
| Variable Set        | Share variables across workspaces  |
| CLI Workflow        | Started locally, executed remotely |
| VCS Workflow        | Git push triggers runs             |
| Remote Operations   | Execution occurs in HCP Terraform  |
| State Migration     | Move local state to remote backend |
| Run Triggers        | Trigger dependent workspace runs   |
| Remote State        | Share outputs between workspaces   |
| Teams               | Manage user access                 |
| Dynamic Credentials | Temporary cloud credentials        |
| Health Assessments  | Workspace health monitoring        |
| Drift Detection     | Detect out-of-band changes         |
| Sentinel            | Policy as Code                     |
| Change Requests     | Approval workflow                  |
| Explorer            | Infrastructure visualization       |

---

# Most Important Exam Questions

1. Difference between Run Trigger and Remote State Data Source.
2. Difference between CLI-Driven and VCS-Driven workflows.
3. Purpose of Variable Sets.
4. Benefits of Remote Operations.
5. What is Drift Detection?
6. What are Dynamic Credentials?
7. What is Sentinel used for?
8. Difference between Project and Workspace.
9. Why use Remote State Storage?
10. Purpose of Teams and Permissions.
