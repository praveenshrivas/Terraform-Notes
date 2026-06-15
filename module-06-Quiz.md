# Terraform Module 06 - HCP Terraform Questions 01

## Question 1

HCP Terraform was previously known as Terraform Cloud.

A. True
B. False

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: A**

Terraform Cloud was renamed to HCP Terraform as part of HashiCorp Cloud Platform.

</details>

---

## Question 2

The primary purpose of HCP Terraform is:

A. Container Orchestration
B. Infrastructure as Code Management
C. Source Code Management
D. Monitoring Infrastructure

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

HCP Terraform provides collaboration, remote execution, state management, and governance for Terraform.

</details>

---

## Question 3

A Workspace in HCP Terraform contains:

A. State File
B. Variables
C. Run History
D. All of the Above

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: D**

A Workspace includes Terraform state, variables, configuration, and run history.

</details>

---

## Question 4

A Project is used to:

A. Execute Terraform Runs
B. Group Multiple Workspaces
C. Store Terraform State
D. Create Variables

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

Projects are organizational units used to group related workspaces.

</details>

---

## Question 5

Variable Sets allow variables to be shared across multiple workspaces.

A. True
B. False

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: A**

Variable Sets provide centralized management of common variables.

</details>

---

## Question 6

Which command is used to authenticate Terraform CLI with HCP Terraform?

A. terraform connect
B. terraform auth
C. terraform login
D. terraform cloud

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: C**

```bash
terraform login
```

Generates and stores an API token.

</details>

---

## Question 7

In a CLI-driven workflow, Terraform runs execute on:

A. GitHub
B. Local Laptop
C. AWS
D. HCP Terraform

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: D**

The commands are initiated locally, but execution occurs remotely in HCP Terraform.

</details>

---

## Question 8

Which workflow automatically starts a Terraform run when code is pushed to GitHub?

A. CLI-driven Workflow
B. VCS-driven Workflow
C. Remote Backend Workflow
D. State-driven Workflow

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

VCS-driven workflows trigger runs automatically from source control events.

</details>

---

## Question 9

Remote Operations means Terraform plans and applies execute on HashiCorp-managed infrastructure.

A. True
B. False

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: A**

Remote Operations execute Terraform runs within HCP Terraform rather than on local machines.

</details>

---

## Question 10

State Migration is used to:

A. Upgrade Terraform Version
B. Move Local State to HCP Terraform
C. Create New Resources
D. Upgrade Providers

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

State Migration transfers existing local state files into HCP Terraform.

</details>

---

## Question 11

What is the primary purpose of Run Triggers?

A. Share Variables
B. Share Outputs
C. Trigger Runs in Another Workspace
D. Store State

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: C**

Run Triggers automatically initiate runs in dependent workspaces.

</details>

---

## Question 12

The terraform_remote_state data source is used to:

A. Trigger Another Workspace
B. Read Outputs from Another Workspace
C. Create Variables
D. Import Resources

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

Remote State Data Source allows workspaces to consume outputs from other workspaces.

</details>

---

## Question 13

Run Triggers and Remote State Data Sources perform the same function.

A. True
B. False

<details>
<summary>Show Answer</summary>

❌ **Correct Answer: B**

Run Triggers trigger execution.
Remote State Data Sources share outputs.

</details>

---

## Question 14

Which HCP Terraform feature helps manage user access?

A. Explorer
B. Variable Sets
C. Teams
D. Run Triggers

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: C**

Teams provide centralized permission management.

</details>

---

## Question 15

The principle of Least Privilege means:

A. Everyone gets Admin Access
B. Users receive only the permissions required
C. All users share credentials
D. No permissions are assigned

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

Least Privilege reduces security risks by granting only necessary permissions.

</details>

---

## Question 16

Dynamic Credentials are preferred because:

A. They never expire
B. They eliminate static cloud credentials
C. They replace Terraform State
D. They improve plan speed

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

Dynamic credentials improve security by avoiding long-lived secrets.

</details>

---

## Question 17

Which of the following is an example of Dynamic Credentials?

A. AWS Secret Key
B. AWS Access Key
C. AWS IAM Role
D. GitHub Token

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: C**

IAM Roles provide temporary credentials instead of static keys.

</details>

---

## Question 18

Health Assessments are used to:

A. Create Infrastructure
B. Upgrade Terraform
C. Monitor Workspace Health
D. Create Variables

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: C**

Health Assessments provide visibility into workspace health and risks.

</details>

---

## Question 19

Infrastructure Drift occurs when:

A. Terraform Version Changes
B. Infrastructure Changes Outside Terraform
C. State File Is Deleted
D. Providers Are Upgraded

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

Drift occurs when actual infrastructure differs from Terraform's state.

</details>

---

## Question 20

Which feature automatically identifies infrastructure drift?

A. Explorer
B. Drift Detection
C. Variable Sets
D. Run Triggers

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

Drift Detection compares actual infrastructure against Terraform state.

</details>

</details>

---

## Question 21

Sentinel is used for:

A. Monitoring Infrastructure
B. State Management
C. Policy as Code
D. Variable Management

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: C**

Sentinel is HashiCorp's Policy as Code framework.

</details>

---

## Question 22

Policies are typically evaluated:

A. After Apply
B. During Destroy
C. Before Apply
D. During Login

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: C**

Policies are checked before infrastructure changes are applied.

</details>

---

## Question 23

Change Requests are used to:

A. Share Variables
B. Approve Infrastructure Changes
C. Store State Files
D. Create Workspaces

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

Change Requests provide governance and approval workflows.

</details>

---

## Question 24

Explorer helps users:

A. Manage Variables
B. View Infrastructure Relationships
C. Execute Terraform Runs
D. Create Policies

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

Explorer visualizes projects, workspaces, and infrastructure relationships.

</details>

---

## Question 25

Which statement best describes HCP Terraform?

A. Configuration Management Tool
B. Managed Terraform Service for Collaboration and Governance
C. Container Runtime
D. Monitoring Platform

<details>
<summary>Show Answer</summary>

✅ **Correct Answer: B**

HCP Terraform provides remote execution, state management, collaboration, governance, and automation.

</details>
