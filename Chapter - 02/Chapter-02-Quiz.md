# Terraform Quiz – Day 2
> Chapter 02: Providers, Dependency Lock File, terraform init, Core/Plugin Architecture, Resources, Terraform State

## Question 1
What do Terraform providers add to a configuration?
- [ ] Only authentication tokens
- [ ] Resource types and/or data sources
- [ ] State file encryption
- [ ] CLI autocomplete rules
<details>
<summary>Answer</summary>
✅ Resource types and/or data sources

Each provider adds a set of resource types and/or data sources that Terraform can manage. Without providers, Terraform can't manage any infrastructure.
</details>

## Question 2
Which Registry tier is owned and maintained directly by HashiCorp?
- [ ] Partner
- [ ] Community
- [ ] Official
- [ ] Archived
<details>
<summary>Answer</summary>
✅ Official

Official providers are owned and maintained by HashiCorp itself (e.g. `hashicorp`, `IBM`, `ansible` namespaces).
</details>

## Question 3
In a provider source address `hashicorp/aws`, if the hostname is omitted, what does Terraform assume?
- [ ] `github.com`
- [ ] `registry.terraform.io`
- [ ] `local.mirror`
- [ ] It errors out and requires an explicit hostname
<details>
<summary>Answer</summary>
✅ `registry.terraform.io`

Hostname defaults to `registry.terraform.io`, the public Terraform Registry, if omitted from the source address.
</details>

## Question 4
What is a provider's "local name" used for?
- [ ] It's only used inside the `required_providers` block
- [ ] It's the identifier used everywhere else in the module to refer to the provider
- [ ] It replaces the need for a `provider` block
- [ ] It sets the provider's version constraint
<details>
<summary>Answer</summary>
✅ It's the identifier used everywhere else in the module to refer to the provider

The local name is module-specific and must be unique per module; outside of `required_providers`, configs always refer to providers by their local name.
</details>

## Question 5
Which version constraint syntax allows only patch releases within a specific minor version (e.g., 1.0.x)?
- [ ] `>= 1.0`
- [ ] `<= 1.0`
- [ ] `~> 1.0.4`
- [ ] `== 1.0`
<details>
<summary>Answer</summary>
✅ `~> 1.0.4`

The `~>` operator allows the rightmost version component to increment — recommended for root modules to avoid accidental major/minor upgrades.
</details>

## Question 6
True or False: Reusable/child modules should typically specify both a minimum AND a maximum version constraint for their providers.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

Reusable modules should specify only a minimum version (`>=`); the root module should manage the maximum version to avoid forcing simultaneous upgrades across many modules.
</details>

## Question 7
What does the `alias` argument in a `provider` block let you do?
- [ ] Rename the provider's resource types
- [ ] Define multiple configurations for the same provider
- [ ] Set a deprecated version constraint
- [ ] Skip provider authentication
<details>
<summary>Answer</summary>
✅ Define multiple configurations for the same provider

`alias` lets you configure the same provider multiple times (e.g., different AWS regions) and reference each with `<PROVIDER>.<ALIAS>`.
</details>

## Question 8
True or False: Child modules automatically inherit provider `source` and `version` requirements from the root module.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

Child modules do NOT inherit provider source/version requirements — only the provider configuration itself is passed down. Requirements must be explicitly declared in the child module.
</details>

## Question 9
What does the dependency lock file (`.terraform.lock.hcl`) track?
- [ ] Provider AND remote module version selections
- [ ] Only provider version selections
- [ ] Only remote module version selections
- [ ] Local variable values
<details>
<summary>Answer</summary>
✅ Only provider version selections

The lock file tracks only provider dependencies. Remote modules always resolve to the newest version matching constraints (use exact version constraints for consistent module selection).
</details>

## Question 10
If a lock file already has a recorded selection for a provider, what does `terraform init` do by default?
- [ ] Always installs the newest available version
- [ ] Always reuses the version recorded in the lock file
- [ ] Prompts the user to choose a version
- [ ] Deletes the lock file and starts over
<details>
<summary>Answer</summary>
✅ Always reuses the version recorded in the lock file

Even if a newer version is available, Terraform re-selects the locked version unless you run `terraform init -upgrade`.
</details>

## Question 11
What do the `h1:` and `zh:` prefixes in the lock file's `hashes` represent?
- [ ] Different provider tiers
- [ ] Different checksum/hashing schemes
- [ ] Different Terraform CLI versions
- [ ] Different registry hostnames
<details>
<summary>Answer</summary>
✅ Different checksum/hashing schemes

`zh:` is the legacy "zip hash" scheme; `h1:` is the current preferred content-based hash scheme. Terraform gradually migrates toward `h1:`.
</details>

## Question 12
What are the four things `terraform init` does, in order?
- [ ] Validate → Plan → Apply → Lock
- [ ] Backend → Modules → Providers → Lock file
- [ ] Providers → Backend → Lock file → Modules
- [ ] Format → Validate → Plan → Apply
<details>
<summary>Answer</summary>
✅ Backend → Modules → Providers → Lock file

`terraform init` initializes the backend, downloads/installs modules, downloads/installs providers, then creates/updates the lock file.
</details>

## Question 13
True or False: The `.terraform` directory should be committed to version control.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

`.terraform` is auto-managed cache for providers/modules and should never be committed or hand-edited — only `.terraform.lock.hcl` should be committed.
</details>

## Question 14
What does Terraform Core communicate with Terraform Plugins over?
- [ ] Shared memory
- [ ] RPC (remote procedure calls)
- [ ] REST over HTTP only
- [ ] Direct function calls in the same process
<details>
<summary>Answer</summary>
✅ RPC (remote procedure calls)

Plugins run as separate processes, and Terraform Core communicates with them over RPC.
</details>

## Question 15
Which of the following is a responsibility of Terraform Core, NOT a plugin?
- [ ] Authenticating with the infrastructure provider
- [ ] Defining managed resources and data sources
- [ ] Constructing the resource graph
- [ ] Executing provisioner scripts on resource creation
<details>
<summary>Answer</summary>
✅ Constructing the resource graph

Terraform Core handles config interpolation, state management, resource graph construction, plan execution, and RPC communication with plugins.
</details>

## Question 16
True or False: Provisioners are always discovered dynamically, just like providers.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

Several provisioners are built into Terraform Core, while providers are always discovered dynamically.
</details>

## Question 17
During plugin selection at `terraform init`, if an acceptable version is already installed locally, what does Terraform do?
- [ ] Always downloads the newest version from the Registry anyway
- [ ] Uses the newest installed version meeting the constraint, even if a newer Registry version exists
- [ ] Fails and requires manual resolution
- [ ] Ignores version constraints entirely
<details>
<summary>Answer</summary>
✅ Uses the newest installed version meeting the constraint, even if a newer Registry version exists

Terraform prefers an already-installed compatible version over checking the Registry for something newer.
</details>

## Question 18
What forms a resource's unique identifier (ID) within a workspace?
- [ ] Only the resource name
- [ ] Resource type + resource name (`type.name`)
- [ ] The provider's source address
- [ ] The order it appears in the `.tf` file
<details>
<summary>Answer</summary>
✅ Resource type + resource name (`type.name`)

E.g., `random_pet.name` — type and name together must be unique within a workspace.
</details>

## Question 19
Which of the following is an example of a Terraform "meta-argument"?
- [ ] `ami`
- [ ] `instance_type`
- [ ] `count`
- [ ] `arn`
<details>
<summary>Answer</summary>
✅ `count`

Meta-arguments (like `count`) change a resource's behavior and are a function of Terraform itself — not resource- or provider-specific.
</details>

## Question 20
What creates an "implicit dependency" between two resources?
- [ ] Placing them in the same file
- [ ] Referencing one resource's attribute inside another resource's argument
- [ ] Using the same provider
- [ ] Naming them alphabetically in order
<details>
<summary>Answer</summary>
✅ Referencing one resource's attribute inside another resource's argument

E.g., `tags = { Name = random_pet.name.id }` creates an implicit dependency — Terraform won't create the referencing resource until the referenced one exists.
</details>

## Question 21
Why does Terraform require a state file instead of just inspecting real-world resources on every run?
- [ ] It's faster to write state files than to query APIs
- [ ] Not all resources/providers support tagging, so state provides a reliable mapping between config and real objects
- [ ] State files are required by cloud provider terms of service
- [ ] Terraform Core cannot function without a local database
<details>
<summary>Answer</summary>
✅ Not all resources/providers support tagging, so state provides a reliable mapping between config and real objects

Early Terraform prototypes tried using tags instead of state but ran into major limitations since not all resources/providers support them.
</details>

## Question 22
What does Terraform state retain that helps determine destroy order when a resource is removed from configuration?
- [ ] The provider's changelog
- [ ] A copy of the most recent set of dependencies (metadata)
- [ ] The Terraform CLI version history
- [ ] Nothing — order is always alphabetical
<details>
<summary>Answer</summary>
✅ A copy of the most recent set of dependencies (metadata)

Since the deleted resource's config no longer exists, Terraform relies on the dependency metadata retained in state to determine correct destroy order.
</details>

## Question 23
True or False: You should manually edit the `.tfstate` file directly when you need to make state changes.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

Never manually edit the state file — this risks drift and unintended destroy/recreate behavior. Use CLI commands instead.
</details>

## Question 24
Which command lists resource addresses currently tracked in state?
- [ ] `terraform show`
- [ ] `terraform state list`
- [ ] `terraform plan`
- [ ] `terraform providers`
<details>
<summary>Answer</summary>
✅ `terraform state list`

`terraform state list` returns resource names/addresses in state — useful for locating a specific resource in larger configurations.
</details>

## Question 25
Which flag/command replaced the deprecated `terraform taint` command?
- [ ] `terraform state mv`
- [ ] `-replace` (on `plan`/`apply`)
- [ ] `terraform refresh`
- [ ] `terraform import`
<details>
<summary>Answer</summary>
✅ `-replace` (on `plan`/`apply`)

`-replace="<address>"` forces destroy + recreate of a specific resource, replacing the older `terraform taint` workflow.
</details>

## Question 26
What does `terraform state mv` update?
- [ ] Only the configuration files
- [ ] Only the state — not the configuration file
- [ ] Both state and configuration simultaneously
- [ ] Only the provider's remote records
<details>
<summary>Answer</summary>
✅ Only the state — not the configuration file

`state mv` moves or renames a resource within state (or between state files) but does not touch your `.tf` configuration.
</details>

## Question 27
What does the `removed` block (introduced in Terraform 1.7) allow you to do?
- [ ] Permanently destroy a resource and its real infrastructure
- [ ] Stop managing a resource in state WITHOUT destroying the real infrastructure
- [ ] Remove a provider from `required_providers`
- [ ] Delete an entire workspace
<details>
<summary>Answer</summary>
✅ Stop managing a resource in state WITHOUT destroying the real infrastructure

With `lifecycle { destroy = false }`, the `removed` block tells Terraform to drop tracking for a resource while leaving the real object intact — replacing the old `terraform state rm`.
</details>

## Question 28
What must already exist in your configuration before running `terraform import`?
- [ ] Nothing — import creates the resource block automatically
- [ ] A matching `resource` block for the object being imported
- [ ] A `removed` block
- [ ] A `data` block only
<details>
<summary>Answer</summary>
✅ A matching `resource` block for the object being imported

`terraform import <address> <id>` requires a corresponding resource block already present in config to attach the imported object to.
</details>

## Question 29
What does `terraform refresh` do?
- [ ] Updates your configuration file to match real infrastructure
- [ ] Syncs the state file with real infrastructure that changed outside Terraform, without touching config
- [ ] Deletes and recreates all resources
- [ ] Installs the latest provider versions
<details>
<summary>Answer</summary>
✅ Syncs the state file with real infrastructure that changed outside Terraform, without touching config

`terraform refresh` updates state to reflect out-of-band changes to real infrastructure; it doesn't modify your `.tf` files. It also runs automatically as part of `plan`/`apply`/`destroy`.
</details>

## Question 30
True or False: Terraform state can include sensitive information, such as passwords or security keys, so it must be stored securely.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ True

State files can contain sensitive data and should be stored securely with restricted access — using remote state (e.g., HCP Terraform) helps manage this at scale.
</details>
