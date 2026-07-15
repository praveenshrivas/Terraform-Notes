# Terraform Quiz – Day 1
> Chapter 01: Introduction to Terraform & IaC, Deployment Workflow, Use Cases

## Question 1
What does IaC stand for?
- [ ] Infrastructure as Configuration
- [ ] Infrastructure as Code
- [ ] Integrated automation Cloud
- [ ] Infrastructure automation Center
<details>
<summary>Answer</summary>
✅ Infrastructure as Code

IaC means defining infrastructure (CPU, memory, disk, firewalls, etc.) as code within human-readable, versionable configuration files.
</details>

## Question 2
Which of the following BEST describes Terraform's configuration language?
- [ ] Imperative — you write step-by-step instructions
- [ ] Declarative — you describe the desired end-state
- [ ] Object-oriented — you define classes of infrastructure
- [ ] Functional — you compose infrastructure from pure functions
<details>
<summary>Answer</summary>
✅ Declarative — you describe the desired end-state

Terraform's language describes the end-state of your infrastructure. You don't write step-by-step instructions; Terraform handles the underlying logic.
</details>

## Question 3
What mechanism does Terraform use to communicate with a cloud platform like AWS or Azure?
- [ ] SSH tunnels
- [ ] Providers, via the platform's API
- [ ] Direct database writes
- [ ] SNMP polling
<details>
<summary>Answer</summary>
✅ Providers, via the platform's API

Providers are plugins that let Terraform interact with cloud platforms, SaaS providers, and other APIs.
</details>

## Question 4
True or False: Terraform can only manage cloud infrastructure, not on-premises resources.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

Terraform manages both cloud and on-prem resources — it lets you define infrastructure across multiple providers and platforms in one consistent workflow.
</details>

## Question 5
Which of the following is NOT one of the three core Terraform workflow stages (conceptual model)?
- [ ] Write
- [ ] Plan
- [ ] Apply
- [ ] Compile
<details>
<summary>Answer</summary>
✅ Compile

The three core stages are Write, Plan, and Apply. Terraform does not "compile" your configuration in this sense.
</details>

## Question 6
In the 5-step practical deployment workflow, what does the "Initialize" step do?
- [ ] Identifies infrastructure needed for the project
- [ ] Installs the provider plugins Terraform needs
- [ ] Executes the planned changes
- [ ] Writes the configuration files
<details>
<summary>Answer</summary>
✅ Installs the provider plugins Terraform needs

`terraform init` installs the plugins required to manage the infrastructure defined in your configuration.
</details>

## Question 7
Fill in the blank: The practical deployment workflow sequence is Scope → Author → _____ → Plan → Apply.
- [ ] Validate
- [ ] Initialize
- [ ] Format
- [ ] Lock
<details>
<summary>Answer</summary>
✅ Initialize

Full sequence: Scope → Author → Initialize → Plan → Apply.
</details>

## Question 8
What file does Terraform use as the "source of truth" to determine what changes are needed to match your configuration?
- [ ] The provider's API response
- [ ] The `.terraform.lock.hcl` file
- [ ] The state file
- [ ] The `.tf` configuration file itself
<details>
<summary>Answer</summary>
✅ The state file

Terraform keeps track of your real infrastructure in a state file, which it uses to determine the changes needed to match your configuration.
</details>

## Question 9
True or False: Terraform's configuration is imperative, meaning you must write step-by-step instructions for how to create each resource.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

Terraform's language is declarative — you describe the desired end-state, and Terraform figures out the steps needed to get there.
</details>

## Question 10
What does Terraform use to determine the correct order of operations and which resources can be created in parallel?
- [ ] Alphabetical order of resource names
- [ ] A resource graph
- [ ] The order resources appear in the `.tf` file
- [ ] A random selection algorithm
<details>
<summary>Answer</summary>
✅ A resource graph

Terraform builds a resource graph to determine dependencies, creating or modifying non-dependent resources in parallel for efficiency.
</details>

## Question 11
What are reusable, shareable collections of Terraform configuration called?
- [ ] Templates
- [ ] Modules
- [ ] Packages
- [ ] Blueprints
<details>
<summary>Answer</summary>
✅ Modules

Modules are reusable configuration components that define configurable collections of infrastructure — available publicly via the Terraform Registry or written yourself.
</details>

## Question 12
Which platform provides shared state, secrets management, RBAC, and a private registry for teams collaborating on Terraform?
- [ ] Terraform Registry
- [ ] HCP Terraform
- [ ] GitHub Actions
- [ ] Terraform Cloud CLI
<details>
<summary>Answer</summary>
✅ HCP Terraform

HCP Terraform runs Terraform in a consistent environment and provides secure access to shared state and secret data, RBAC, and a private registry.
</details>

## Question 13
True or False: Applying the same Terraform configuration multiple times without changes will produce the same result each time (idempotency).
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ True

Idempotency is a core IaC property — applying the same code repeatedly produces the same result.
</details>

## Question 14
What is the main risk of scaling infrastructure manually (without IaC) across many servers?
- [ ] Increased cloud costs only
- [ ] Process inconsistencies and configuration drift between servers
- [ ] Slower network speeds
- [ ] Reduced provider API rate limits
<details>
<summary>Answer</summary>
✅ Process inconsistencies and configuration drift between servers

Manual, session-by-session changes often result in skipped steps or slight variations, which compound over time and can affect performance, usability, or security.
</details>

## Question 15
Which use case describes using Terraform to manage resources across AWS and Azure simultaneously with one consistent workflow?
- [ ] Self-service clusters
- [ ] Multi-cloud deployment
- [ ] Software defined networking
- [ ] Parallel environments
<details>
<summary>Answer</summary>
✅ Multi-cloud deployment

Terraform lets you use the same workflow to manage multiple providers and handle cross-cloud dependencies, increasing fault tolerance.
</details>

## Question 16
What is Sentinel, in the context of Terraform use cases?
- [ ] A monitoring dashboard for Terraform runs
- [ ] A policy-as-code framework for automated compliance/governance enforcement
- [ ] A backup tool for state files
- [ ] A CLI plugin for AWS resources
<details>
<summary>Answer</summary>
✅ A policy-as-code framework for automated compliance/governance enforcement

Sentinel (available in Terraform Enterprise/HCP Terraform) automatically enforces policies before Terraform makes infrastructure changes, removing ticket-based review bottlenecks.
</details>

## Question 17
Which Terraform use case involves automatically generating Terraform configuration when a service registers with Consul?
- [ ] Kubernetes management
- [ ] Consul-Terraform-Sync / Network Infrastructure Automation (NIA)
- [ ] Self-service clusters
- [ ] PaaS application setup
<details>
<summary>Answer</summary>
✅ Consul-Terraform-Sync / Network Infrastructure Automation (NIA)

CTS/NIA automatically generates Terraform configuration to expose ports and adjust network settings when a service registers with Consul.
</details>

## Question 18
True or False: Terraform can only deploy a Kubernetes cluster, but cannot manage resources (pods, deployments, services) within that cluster.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

Terraform can both deploy a Kubernetes cluster AND manage its resources (pods, deployments, services, etc.) via the Kubernetes provider.
</details>

## Question 19
In the N-tier application infrastructure use case, if a web server tier depends on a database tier, what does Terraform do?
- [ ] Creates both tiers simultaneously regardless of dependency
- [ ] Provisions the database tier before the web server tier
- [ ] Requires the user to manually specify the order every time
- [ ] Ignores the dependency and lets the provider handle ordering
<details>
<summary>Answer</summary>
✅ Provisions the database tier before the web server tier

Terraform automatically handles dependencies between tiers, deploying the database tier before the web servers that depend on it.
</details>

## Question 20
What is the "self-service clusters" use case primarily solving for?
- [ ] Reducing cloud spend
- [ ] Letting product teams provision compliant infrastructure independently, reducing repetitive requests to a central ops team
- [ ] Automating Kubernetes pod scaling
- [ ] Enabling multi-region failover
<details>
<summary>Answer</summary>
✅ Letting product teams provision compliant infrastructure independently, reducing repetitive requests to a central ops team

Modules codify organizational standards so teams can self-serve compliant infrastructure, and HCP Terraform can integrate with ticketing systems like ServiceNow.
</details>

## Question 21
Which of the following is TRUE about "parallel environments" as a Terraform use case?
- [ ] They are meant to be maintained indefinitely for cost savings
- [ ] They allow rapid creation/decommissioning of dev, test, QA, and prod-like environments
- [ ] They only apply to production environments
- [ ] They require a separate Terraform binary per environment
<details>
<summary>Answer</summary>
✅ They allow rapid creation/decommissioning of dev, test, QA, and prod-like environments

Creating disposable environments as needed is more cost-efficient than maintaining each one indefinitely.
</details>

## Question 22
What does the HCP Terraform Operator for Kubernetes allow you to do?
- [ ] Directly edit Kubernetes YAML files
- [ ] Manage cloud and on-prem infrastructure through a Kubernetes Custom Resource Definition (CRD)
- [ ] Replace the Kubernetes API server
- [ ] Automatically write Terraform provider code
<details>
<summary>Answer</summary>
✅ Manage cloud and on-prem infrastructure through a Kubernetes Custom Resource Definition (CRD)

The Operator lets you manage HCP Terraform workspaces and provision infrastructure via Kubernetes CRDs.
</details>

## Question 23
True or False: In the core Terraform workflow, `terraform apply` will always execute changes immediately without ever showing a plan for approval.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

By default, `terraform apply` generates a plan and prompts for approval before making changes — unless you pass a saved plan file or use `-auto-approve`.
</details>

## Question 24
When working as a team (rather than as an individual), what is the recommended way to review Terraform's planned changes before merging?
- [ ] Ask each teammate to run `terraform apply` locally and compare
- [ ] Review speculative plan output alongside pull requests
- [ ] Skip review and merge directly to the main branch
- [ ] Only review the applied state after the fact
<details>
<summary>Answer</summary>
✅ Review speculative plan output alongside pull requests

This lets the team evaluate intent and risk before merging, either manually attaching plan output or having CI post it automatically.
</details>

## Question 25
Why might the "final concrete plan" (run after a PR is merged) differ from the speculative plan reviewed during PR review?
- [ ] Terraform randomizes plan output between runs
- [ ] Merge order or infrastructure changes made outside of that PR since the speculative plan was generated
- [ ] The final plan always ignores state
- [ ] Speculative plans are not real plans at all
<details>
<summary>Answer</summary>
✅ Merge order or infrastructure changes made outside of that PR since the speculative plan was generated

For example, a manual change to infrastructure after the plan was reviewed could cause the final plan to differ.
</details>

## Question 26
Which of the following BEST describes "cost estimation" as it relates to Terraform use cases?
- [ ] A feature that blocks all infrastructure changes above a certain cost
- [ ] A feature/policy mechanism to define limits on costs associated with infrastructure changes
- [ ] A third-party tool unrelated to Terraform
- [ ] A billing dashboard built into Terraform Core
<details>
<summary>Answer</summary>
✅ A feature/policy mechanism to define limits on costs associated with infrastructure changes

Cost estimation works alongside policies (such as Sentinel) to help manage spend tied to infrastructure changes.
</details>

## Question 27
True or False: Terraform's use of a resource graph allows it to create non-dependent resources in parallel for efficiency.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ True

The resource graph determines dependencies, and Terraform creates or modifies non-dependent resources in parallel.
</details>

## Question 28
Which use case involves using Terraform to codify setup for a platform like Heroku, plus supporting DNS and CDN services?
- [ ] Kubernetes
- [ ] PaaS Application Setup
- [ ] Policy Compliance and Management
- [ ] Software Demos
<details>
<summary>Answer</summary>
✅ PaaS Application Setup

Terraform can codify Heroku app setup along with DNSimple (DNS) and Cloudflare (CDN) — consistently, without a web interface.
</details>

## Question 29
What is one key advantage of provisioning infrastructure across multiple cloud providers (multi-cloud deployment)?
- [ ] It guarantees lower costs
- [ ] Increased fault tolerance and more graceful recovery from a single provider's outage
- [ ] It eliminates the need for a state file
- [ ] It removes the need for provider authentication
<details>
<summary>Answer</summary>
✅ Increased fault tolerance and more graceful recovery from a single provider's outage

This is the primary benefit, though it does add complexity since each provider has its own interfaces and workflows.
</details>

## Question 30
True or False: HCP Terraform's free tier supports up to 5 users.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ True

HCP Terraform is free for up to 5 users, and lets you securely share state with teammates.
</details>
