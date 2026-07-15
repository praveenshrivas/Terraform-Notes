# Chapter 01 - Part 03: Terraform Use Cases

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> Builds on [Part 01](./01-intro-to-terraform-and-iac.md) and [Part 02](./01-terraform-deployment-workflow.md).

---

## Overview

Terraform's provider-agnostic, declarative workflow makes it applicable across many infrastructure scenarios. Below are the common use cases referenced in official docs/exam material.

| Use Case | Key Idea |
|---|---|
| **Multi-Cloud Deployment** | One consistent workflow to manage resources and cross-cloud dependencies across multiple providers — increases fault tolerance and eases recovery from a single provider's outage. |
| **Application Infrastructure (N-tier)** | Manages multi-tier app infra (web, API, cache, DB, routing) as one config. Terraform auto-handles cross-tier dependencies — e.g., provisions the database tier before the web servers that depend on it. |
| **Self-Service Clusters** | Central ops teams package standards into reusable **modules**, letting product teams provision their own compliant infrastructure independently. HCP Terraform can integrate with ticketing systems (e.g., ServiceNow) to auto-generate infra requests. |
| **Policy Compliance & Management** | **Sentinel** (policy-as-code, available in Terraform Enterprise/HCP Terraform) enforces governance/compliance automatically before changes are applied — removes ticket-based review bottlenecks. Cost estimation can also enforce spend limits. |
| **PaaS Application Setup** | Codifies setup for PaaS platforms (e.g., Heroku) plus supporting services (DNS via DNSimple, CDN via Cloudflare) — consistent setup without manual web-console steps. |
| **Software Defined Networking (SDN)** | Terraform automates SDN configuration. **Consul-Terraform-Sync (CTS)** — part of Network Infrastructure Automation (NIA) — auto-generates Terraform config when a service registers with Consul, shifting network changes from ticket-based to automated. |
| **Kubernetes** | Terraform can both **deploy** a Kubernetes cluster and **manage in-cluster resources** (pods, deployments, services). The **HCP Terraform Operator** manages infra via a Kubernetes CRD. |
| **Parallel Environments** | Rapidly spin up/tear down disposable dev, test, QA, and prod-like environments — cheaper than maintaining every environment indefinitely. |
| **Software Demos** | Provision and bootstrap demo environments on any cloud so end users can try software on their own infra, adjustable at any scale. |

---

## Terms Worth Remembering

- **Sentinel** — HashiCorp's policy-as-code framework for automated compliance/governance enforcement (Terraform Enterprise / HCP Terraform only).
- **Consul-Terraform-Sync (CTS) / NIA (Network Infrastructure Automation)** — automates SDN/network config changes in response to Consul service registration events.
- **HCP Terraform Operator** — lets Kubernetes manage HCP Terraform workspaces via a Custom Resource Definition (CRD).

---

## Quick Recap (for revision)

- Multi-cloud, N-tier apps, self-service infra, policy compliance (Sentinel), PaaS setup, SDN automation (CTS/NIA), Kubernetes, parallel/disposable environments, and software demos are the core documented use cases.
- **Sentinel = policy-as-code**; **CTS = network automation tied to Consul**; **HCP Terraform Operator = Kubernetes-native management**.
