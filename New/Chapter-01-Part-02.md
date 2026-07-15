# Chapter 01 - Part 02: The Terraform Deployment Workflow

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> Builds on [Part 01](./01-intro-to-terraform-and-iac.md) — only new material covered here.

---

## 1. The 5-Stage Deployment Workflow

Part 01 covered the high-level **3-stage** workflow (Write → Plan → Apply). The official Get Started tutorial breaks this down further into **5 concrete steps** used when actually deploying infrastructure:

| Step | Description |
|---|---|
| **Scope** | Identify the infrastructure needed for the project |
| **Author** | Write the configuration for that infrastructure |
| **Initialize** | Run `terraform init` — installs the provider plugins Terraform needs |
| **Plan** | Preview the changes Terraform will make to match the configuration |
| **Apply** | Execute the planned changes |

> **Exam tip:** Write/Plan/Apply is the conceptual model; Scope/Author/Initialize/Plan/Apply is the practical execution sequence — **Initialize** is the key addition (installs plugins/providers before a plan can run).

---

## 2. Collaboration Details (new specifics)

- HCP Terraform is **free for up to 5 users**.
- Remote state backends let teams **securely share state**.
- HCP Terraform prevents **race conditions** when multiple people apply configuration changes at the same time.
- HCP Terraform can connect to VCS (GitHub, GitLab, etc.) to **automatically propose infrastructure changes** on config commits — enabling a GitOps-style workflow for infrastructure.

---

## 3. Minor Additional Facts

- HashiCorp + community have written **1,000+ providers** (this figure varies slightly by source — "thousands" in the docs page, "over 1,000" in the tutorial).
- Terraform providers **automatically calculate dependencies** between resources to determine correct create/destroy order (same resource-graph concept from Part 01, restated as a provider-level responsibility).

---

## Quick Recap (for revision)

- Practical workflow = **Scope → Author → Initialize → Plan → Apply**
- `terraform init` = installs required provider plugins
- HCP Terraform = free ≤5 users; adds shared state, race-condition prevention, VCS-triggered runs
