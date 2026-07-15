# Chapter 01 Quiz — Introduction to Terraform & IaC

> HashiCorp Certified: Terraform Associate (004) — Practice Quiz
> Covers: What is Terraform/IaC, Deployment Workflow, Use Cases
> 30 questions — multiple choice unless noted. Answer key at the end.

---

**1.** What does IaC stand for?
A) Infrastructure as Configuration
B) Infrastructure as Code
C) Integrated automation Cloud
D) Infrastructure automation Center

**2.** Which of the following BEST describes Terraform's configuration language?
A) Imperative — you write step-by-step instructions
B) Declarative — you describe the desired end-state
C) Object-oriented — you define classes of infrastructure
D) Functional — you compose infrastructure from pure functions

**3.** What mechanism does Terraform use to communicate with a cloud platform like AWS or Azure?
A) SSH tunnels
B) Providers, via the platform's API
C) Direct database writes
D) SNMP polling

**4.** True or False: Terraform can only manage cloud infrastructure, not on-premises resources.

**5.** Which of the following is NOT one of the three core Terraform workflow stages (conceptual model)?
A) Write
B) Plan
C) Apply
D) Compile

**6.** In the 5-step practical deployment workflow, what does the "Initialize" step do?
A) Identifies infrastructure needed for the project
B) Installs the provider plugins Terraform needs
C) Executes the planned changes
D) Writes the configuration files

**7.** Fill in the blank: The practical deployment workflow sequence is Scope → Author → _____ → Plan → Apply.

**8.** What file does Terraform use as the "source of truth" to determine what changes are needed to match your configuration?
A) The provider's API response
B) The `.terraform.lock.hcl` file
C) The state file
D) The `.tf` configuration file itself

**9.** True or False: Terraform's configuration is imperative, meaning you must write step-by-step instructions for how to create each resource.

**10.** What does Terraform use to determine the correct order of operations and which resources can be created in parallel?
A) Alphabetical order of resource names
B) A resource graph
C) The order resources appear in the `.tf` file
D) A random selection algorithm

**11.** What are reusable, shareable collections of Terraform configuration called?
A) Templates
B) Modules
C) Packages
D) Blueprints

**12.** Which platform provides shared state, secrets management, RBAC, and a private registry for teams collaborating on Terraform?
A) Terraform Registry
B) HCP Terraform
C) GitHub Actions
D) Terraform Cloud CLI

**13.** True or False: Applying the same Terraform configuration multiple times without changes will produce the same result each time (idempotency).

**14.** What is the main risk of scaling infrastructure manually (without IaC) across many servers?
A) Increased cloud costs only
B) Process inconsistencies and configuration drift between servers
C) Slower network speeds
D) Reduced provider API rate limits

**15.** Which use case describes using Terraform to manage resources across AWS and Azure simultaneously with one consistent workflow?
A) Self-service clusters
B) Multi-cloud deployment
C) Software defined networking
D) Parallel environments

**16.** What is Sentinel, in the context of Terraform use cases?
A) A monitoring dashboard for Terraform runs
B) A policy-as-code framework for automated compliance/governance enforcement
C) A backup tool for state files
D) A CLI plugin for AWS resources

**17.** Which Terraform use case involves automatically generating Terraform configuration when a service registers with Consul?
A) Kubernetes management
B) Consul-Terraform-Sync / Network Infrastructure Automation (NIA)
C) Self-service clusters
D) PaaS application setup

**18.** True or False: Terraform can only deploy a Kubernetes cluster, but cannot manage resources (pods, deployments, services) within that cluster.

**19.** In the N-tier application infrastructure use case, if a web server tier depends on a database tier, what does Terraform do?
A) Creates both tiers simultaneously regardless of dependency
B) Provisions the database tier before the web server tier
C) Requires the user to manually specify the order every time
D) Ignores the dependency and lets the provider handle ordering

**20.** What is the "self-service clusters" use case primarily solving for?
A) Reducing cloud spend
B) Letting product teams provision compliant infrastructure independently, reducing repetitive requests to a central ops team
C) Automating Kubernetes pod scaling
D) Enabling multi-region failover

**21.** Which of the following is TRUE about "parallel environments" as a Terraform use case?
A) They are meant to be maintained indefinitely for cost savings
B) They allow rapid creation/decommissioning of dev, test, QA, and prod-like environments
C) They only apply to production environments
D) They require a separate Terraform binary per environment

**22.** What does the HCP Terraform Operator for Kubernetes allow you to do?
A) Directly edit Kubernetes YAML files
B) Manage cloud and on-prem infrastructure through a Kubernetes Custom Resource Definition (CRD)
C) Replace the Kubernetes API server
D) Automatically write Terraform provider code

**23.** True or False: In the core Terraform workflow, `terraform apply` will always execute changes immediately without ever showing a plan for approval.

**24.** When working as a team (rather than as an individual), what is the recommended way to review Terraform's planned changes before merging?
A) Ask each teammate to run `terraform apply` locally and compare
B) Review speculative plan output alongside pull requests
C) Skip review and merge directly to the main branch
D) Only review the applied state after the fact

**25.** Why might the "final concrete plan" (run after a PR is merged) differ from the speculative plan reviewed during PR review?
A) Terraform randomizes plan output between runs
B) Merge order or infrastructure changes made outside of that PR since the speculative plan was generated
C) The final plan always ignores state
D) Speculative plans are not real plans at all

**26.** Which of the following BEST describes "cost estimation" as it relates to Terraform use cases?
A) A feature that blocks all infrastructure changes above a certain cost
B) A feature/policy mechanism to define limits on costs associated with infrastructure changes
C) A third-party tool unrelated to Terraform
D) A billing dashboard built into Terraform Core

**27.** True or False: Terraform's use of a resource graph allows it to create non-dependent resources in parallel for efficiency.

**28.** Which use case involves using Terraform to codify setup for a platform like Heroku, plus supporting DNS and CDN services?
A) Kubernetes
B) PaaS Application Setup
C) Policy Compliance and Management
D) Software Demos

**29.** What is one key advantage of provisioning infrastructure across multiple cloud providers (multi-cloud deployment)?
A) It guarantees lower costs
B) Increased fault tolerance and more graceful recovery from a single provider's outage
C) It eliminates the need for a state file
D) It removes the need for provider authentication

**30.** True or False: HCP Terraform's free tier supports up to 5 users.

---

## Answer Key

1. B — Infrastructure as Code
2. B — Declarative
3. B — Providers, via the platform's API
4. False — Terraform manages both cloud and on-prem resources
5. D — Compile is not one of the three core stages (Write, Plan, Apply)
6. B — Installs the provider plugins Terraform needs
7. Initialize
8. C — The state file
9. False — Terraform's language is declarative, not imperative
10. B — A resource graph
11. B — Modules
12. B — HCP Terraform
13. True — idempotency is a core IaC/Terraform property
14. B — Process inconsistencies and configuration drift between servers
15. B — Multi-cloud deployment
16. B — A policy-as-code framework for automated compliance/governance enforcement
17. B — Consul-Terraform-Sync / Network Infrastructure Automation (NIA)
18. False — Terraform can deploy a cluster AND manage in-cluster resources
19. B — Provisions the database tier before the web server tier
20. B — Letting product teams provision compliant infrastructure independently
21. B — They allow rapid creation/decommissioning of environments
22. B — Manage cloud/on-prem infrastructure through a Kubernetes CRD
23. False — `apply` shows a plan and prompts for approval unless using a saved plan or `-auto-approve`
24. B — Review speculative plan output alongside pull requests
25. B — Merge order or infra changes made since the speculative plan was generated
26. B — A feature/policy mechanism to define cost-related limits on infrastructure changes
27. True — non-dependent resources can be created in parallel via the resource graph
28. B — PaaS Application Setup
29. B — Increased fault tolerance and more graceful recovery from a single provider's outage
30. True — HCP Terraform is free for up to 5 users
