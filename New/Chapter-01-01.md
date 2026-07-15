# Chapter 01 - Part 01: Introduction to Terraform & IaC

> HashiCorp Certified: Terraform Associate (004) — Study Notes

---

## 1. What is Terraform?

Terraform is an **Infrastructure as Code (IaC)** tool that lets you define both cloud and on-prem resources in **human-readable configuration files**. These files can be versioned, reused, and shared, giving you a consistent workflow to provision and manage infrastructure across its entire lifecycle.

- Manages **low-level components**: compute, storage, networking
- Manages **high-level components**: DNS entries, SaaS features
- Works across **multiple providers** in a single configuration (multi-cloud/hybrid)

---

## 2. What is Infrastructure as Code (IaC)?

IaC means defining infrastructure (CPU, memory, disk, firewalls, etc.) as **code**, instead of manually configuring it through a GUI or CLI session.

**Evolution of infrastructure management:**
1. Manual changes via web console / CLI (no IaC) — common when teams "lift and shift" on-prem habits to the cloud
2. Scripted commands — hard for humans to read, error-prone
3. Modern IaC tools (Terraform) — both human- and machine-readable, testable, trackable, reusable via modules

### Why it matters
Poor infrastructure lifecycle management carries real risk — financial loss, reputational damage, and in critical systems (gov/military), even safety risk. IaC is a key mitigation.

---

## 3. Day 0 vs Day 1 Activities

| Stage | Description |
|---|---|
| **Day 0** | Initial provisioning and configuration of infrastructure |
| **Day 1 → N** | Ongoing OS/application configuration after initial build (patches, updates, app config) |

- If infrastructure never changes post-build, Day 0-only tooling may be sufficient.
- For ongoing Day 1+ configuration, Terraform is often paired with tools like **Chef, Ansible, Docker**.

Example — Day 0 provisioner (installs & starts a web server):
```hcl
provisioner "remote-exec" {
  inline = [
    "sudo apt-get -y update",
    "sudo apt-get -y install nginx",
    "sudo service nginx start"
  ]
}
```

Example — Day 1+ handoff to a config management tool:
```hcl
provider "chef" {
  server_url = "https://api.chef.io/organization/example"
  run_list   = ["recipe[example]"]
}
```

---

## 4. How Terraform Works

Terraform creates and manages resources through each platform's **API**. A **provider** is what enables Terraform to talk to a given platform/service.

- Thousands of community + HashiCorp-maintained providers exist (AWS, Azure, GCP, Kubernetes, Helm, GitHub, DataDog, Splunk, etc.)
- All published on the **Terraform Registry**
- You can also write custom providers for any service with an accessible API

### Core Terraform Workflow

| Step | What Happens |
|---|---|
| **Write** | Define resources in configuration (can span multiple providers/services) |
| **Plan** | Terraform builds an execution plan — what it will create, update, or destroy — by comparing config to current state |
| **Apply** | On approval, Terraform executes the plan in dependency order |

> Example: If you change a VPC's properties **and** the number of VMs in it, Terraform recreates the VPC *before* scaling the VMs — dependency order is respected.

---

## 5. Why Terraform? (Core Value Props)

| Benefit | Detail |
|---|---|
| **Manage any infrastructure** | Providers cover most platforms; write your own if needed. Terraform favors an **immutable** infrastructure approach, simplifying upgrades. |
| **Track infrastructure** | Generates a plan for approval before changes. Maintains a **state file** — the source of truth used to diff desired vs. actual infrastructure. |
| **Automate changes** | Configuration is **declarative** (describes end state, not steps). Terraform builds a **resource graph** to identify dependencies and parallelize non-dependent resource changes. |
| **Standardize configuration** | Reusable **modules** package configurable infrastructure collections — use public ones from the Registry or write your own. |
| **Collaborate** | Config lives in version control (Git). **HCP Terraform** adds shared state, secrets, RBAC, and a private module/provider registry for team workflows. |

---

## 6. IaC Reliability Benefits (Key Exam Concepts)

- **Idempotent**: applying the same code multiple times produces the same result
- **Consistent & repeatable**: removes manual, session-by-session variation between operators
- **Predictable**: test/review code before applying to production; iterate until output matches expectations
- **Auditable**: version-controlled code means infrastructure evolution can be reviewed over time (Git history)

**Without IaC**, scaling infrastructure manually (multiple SSH sessions, repeated manual commands) introduces:
- Skipped steps
- Inconsistent configurations between servers
- Higher risk with larger teams, since individuals don't always execute instructions identically

**With IaC**, scaling is a matter of revising code (e.g., increasing instance count) — Terraform diffs current vs. desired state and applies only the necessary changes, leaving valid existing infrastructure untouched.

---

## Quick Recap (for revision)

- Terraform = IaC tool → human-readable config → provision/manage infra via provider APIs
- Workflow = **Write → Plan → Apply**
- State file = source of truth for real infrastructure
- Config is **declarative**; Terraform resolves execution order via a **resource graph**
- **Modules** = reusable, shareable infrastructure definitions
- IaC benefits: idempotency, consistency, predictability, auditability
- Day 0 = initial build; Day 1+ = ongoing config (often paired with Chef/Ansible/Docker)
