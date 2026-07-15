# Terraform Quiz – Day 3
> Chapter 03: Core Workflow at Scale, terraform plan, terraform apply, validate & Troubleshooting, Dependency Graph, destroy, fmt

## Question 1
In a team workflow, where do speculative plans typically get reviewed before merging?
- [ ] In a private Slack DM
- [ ] Alongside pull requests in version control
- [ ] Only after the change is already applied
- [ ] In the Terraform Registry
<details>
<summary>Answer</summary>
✅ Alongside pull requests in version control

Reviewing speculative plan output alongside PRs lets teammates evaluate intent and risk before merging — either attached manually or posted automatically by CI.
</details>

## Question 2
True or False: The final concrete plan run after a PR merges is guaranteed to be identical to the speculative plan reviewed during the PR.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

The final plan can differ due to merge order or infrastructure changes made outside the PR since the speculative plan was generated — always re-check before applying.
</details>

## Question 3
What does `terraform plan` do to your infrastructure by itself?
- [ ] Immediately applies the proposed changes
- [ ] Nothing — it only previews changes, without making them
- [ ] Deletes the state file
- [ ] Destroys all resources
<details>
<summary>Answer</summary>
✅ Nothing — it only previews changes, without making them

`plan` reads state, compares to config, and proposes changes — only `apply` actually executes them.
</details>

## Question 4
Which flag saves a plan to a file so it can be applied exactly later, even across different machines?
- [ ] `-save`
- [ ] `-out`
- [ ] `-lock`
- [ ] `-export`
<details>
<summary>Answer</summary>
✅ `-out`

`terraform plan -out <file>` saves the plan; applying that file guarantees only the reviewed changes are made — key for CI/CD pipelines.
</details>

## Question 5
In a saved plan's JSON output, which field records the exact provider versions used to generate the plan?
- [ ] `resource_changes`
- [ ] `configuration`
- [ ] `planned_values`
- [ ] `diagnostics`
<details>
<summary>Answer</summary>
✅ `configuration`

The `configuration` object is a snapshot of config at plan time, including provider version constraints from the lock file.
</details>

## Question 6
True or False: Sensitive input variable values are redacted (not stored) in a saved plan file.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

Even sensitive variables are stored in PLAINTEXT in the plan file — never commit plan files to version control.
</details>

## Question 7
Which planning mode creates a plan whose goal is only to update state/output values to match out-of-band infrastructure changes, without modifying resources?
- [ ] Normal mode
- [ ] Destroy mode
- [ ] Refresh-only mode
- [ ] Target mode
<details>
<summary>Answer</summary>
✅ Refresh-only mode

Activated with `-refresh-only`; useful for reconciling Terraform's records after manually changing infrastructure (e.g. during an incident).
</details>

## Question 8
True or False: `-refresh=false` can be combined with `-refresh-only` mode.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

You cannot use `-refresh=false` in refresh-only mode, since that would effectively disable the entire purpose of the operation.
</details>

## Question 9
What is the recommended alternative to routine use of `-target` for managing large configurations?
- [ ] Always use `-target` for every apply
- [ ] Break large configurations into smaller, independently-applied configurations
- [ ] Disable state locking
- [ ] Use `-auto-approve` instead
<details>
<summary>Answer</summary>
✅ Break large configurations into smaller, independently-applied configurations

`-target` is for exceptional recovery/debugging only — using it routinely risks undetected drift. Data sources can bridge separate configs.
</details>

## Question 10
Which exit code does `-detailed-exitcode` return when the plan succeeded and contains changes?
- [ ] 0
- [ ] 1
- [ ] 2
- [ ] 3
<details>
<summary>Answer</summary>
✅ 2

`0` = no changes, `1` = error, `2` = succeeded with changes present — useful for CI branching logic.
</details>

## Question 11
What are the three node types in Terraform's dependency graph?
- [ ] Input, Output, Local
- [ ] Resource Node, Provider Configuration Node, Resource Meta-Node
- [ ] Plan Node, Apply Node, State Node
- [ ] Root, Branch, Leaf
<details>
<summary>Answer</summary>
✅ Resource Node, Provider Configuration Node, Resource Meta-Node

Resource nodes represent individual resources (one per `count` index); provider config nodes represent provider setup; meta-nodes group resources with `count > 1` for convenience.
</details>

## Question 12
By default, how many nodes can Terraform process concurrently while walking the graph?
- [ ] 1
- [ ] 5
- [ ] 10
- [ ] Unlimited
<details>
<summary>Answer</summary>
✅ 10

Default parallelism is 10 concurrent nodes, adjustable (rarely needed) via `-parallelism=n`.
</details>

## Question 13
True or False: Terraform's `-parallelism` flag is the primary mechanism for handling cloud provider API rate limits.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

Providers like AWS handle rate limiting themselves via backoff/retry in their API clients — `-parallelism` is not designed to address rate limits directly.
</details>

## Question 14
When a resource is being destroyed and recreated, why does the dependency graph split it into two nodes?
- [ ] To make the graph visually simpler
- [ ] Because destroy order often differs from create order
- [ ] Because Terraform Core requires exactly 2 nodes per resource
- [ ] To support multi-cloud deployments
<details>
<summary>Answer</summary>
✅ Because destroy order often differs from create order

A single graph node can't represent two different orderings, so Terraform splits it into a destroy node and a create node.
</details>

## Question 15
What is the sequence of steps `terraform apply` performs after you approve a plan?
- [ ] Apply → Plan → Lock → Unlock
- [ ] Lock state → create/execute plan → update state → unlock state → report changes
- [ ] Validate → Format → Destroy → Apply
- [ ] Unlock → Plan → Lock → Apply
<details>
<summary>Answer</summary>
✅ Lock state → create/execute plan → update state → unlock state → report changes

This is the standard apply sequence — state is locked first to prevent concurrent runs, then unlocked after the update completes.
</details>

## Question 16
True or False: Terraform automatically rolls back a partially-completed apply if it encounters an error.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

There is NO automatic rollback — Terraform logs the error, updates state with whatever succeeded, unlocks state, and exits. You must fix the issue and re-apply.
</details>

## Question 17
What message does Terraform show if a resource was changed or deleted outside of Terraform since the last apply?
- [ ] "Configuration drift detected — aborting"
- [ ] "Objects have changed outside of Terraform"
- [ ] "State file corrupted"
- [ ] Nothing — Terraform silently ignores it
<details>
<summary>Answer</summary>
✅ "Objects have changed outside of Terraform"

Terraform auto-refreshes and reports this before proposing a reconciling plan.
</details>

## Question 18
In "Saved Plan Mode" (`terraform apply <planfile>`), can you add additional planning modes or options like `-var`?
- [ ] Yes, always
- [ ] No — the plan file already locked in those decisions
- [ ] Only `-var` is allowed
- [ ] Only `-target` is allowed
<details>
<summary>Answer</summary>
✅ No — the plan file already locked in those decisions

Saved Plan Mode executes exactly what's in the file with no prompt; extra planning flags only apply in Automatic Plan Mode (no saved plan file).
</details>

## Question 19
What must accompany `-json` when running `terraform apply`?
- [ ] `-no-color`
- [ ] Either `-auto-approve` or a saved plan file
- [ ] `-compact-warnings`
- [ ] Nothing extra is required
<details>
<summary>Answer</summary>
✅ Either `-auto-approve` or a saved plan file

JSON output mode can't sit and wait for an interactive yes/no prompt, so it requires one of these to proceed non-interactively.
</details>

## Question 20
What does `terraform validate` check?
- [ ] Remote state and live provider APIs
- [ ] Syntax validity and internal consistency of configuration only
- [ ] Cost of the proposed infrastructure changes
- [ ] Whether resources already exist in the cloud
<details>
<summary>Answer</summary>
✅ Syntax validity and internal consistency of configuration only

`validate` does NOT validate remote services (state backend, provider APIs) — that's what `plan` does via its implied validation.
</details>

## Question 21
Which command initializes a working directory for validation WITHOUT accessing any configured backend?
- [ ] `terraform init -backend=false`
- [ ] `terraform validate -no-backend`
- [ ] `terraform plan -local`
- [ ] `terraform init -offline`
<details>
<summary>Answer</summary>
✅ `terraform init -backend=false`

This initializes plugins/modules without touching the configured backend — useful for pure syntax validation.
</details>

## Question 22
In the 4-layer troubleshooting model, which layer would a "Cycle" error most likely fall under?
- [ ] Provider layer
- [ ] Core layer (dependency graph construction)
- [ ] State layer
- [ ] Language layer only
<details>
<summary>Answer</summary>
✅ Core layer (dependency graph construction)

Cycle errors are circular logic in the dependency graph — though they surface via `terraform validate`, they stem from how Core builds the graph based on your resource references.
</details>

## Question 23
What causes a "Cycle" error between two resources (e.g., two security groups)?
- [ ] Using the same provider twice
- [ ] The resources reference each other directly within their own configuration blocks
- [ ] Missing a `required_providers` block
- [ ] Having too many `count` instances
<details>
<summary>Answer</summary>
✅ The resources reference each other directly within their own configuration blocks

Mutual references mean neither resource can be created first. Fix by decoupling into a separate relationship resource (e.g., `aws_security_group_rule`).
</details>

## Question 24
Why does `for_each = aws_security_group.*.id` fail with an "Invalid reference" error?
- [ ] `for_each` requires a list, but splat produces a map
- [ ] `for_each` requires a map (or set of strings), but splat expressions produce a list
- [ ] Splat expressions are deprecated
- [ ] `for_each` cannot reference resources at all
<details>
<summary>Answer</summary>
✅ `for_each` requires a map (or set of strings), but splat expressions produce a list

Fix: use a local value to build a map, then reference `each.value` (not `each.id`).
</details>

## Question 25
Once a resource has `for_each` set, how must its attributes be referenced in outputs?
- [ ] Directly, e.g. `aws_instance.web_app.id`
- [ ] Using a `for` expression across all instances, e.g. `[for i in aws_instance.web_app : i.id]`
- [ ] They cannot be referenced in outputs at all
- [ ] Only via `terraform state show`
<details>
<summary>Answer</summary>
✅ Using a `for` expression across all instances, e.g. `[for i in aws_instance.web_app : i.id]`

A direct reference is ambiguous once `for_each`/`count` is set — Terraform doesn't know which instance you mean.
</details>

## Question 26
Which environment variable enables Terraform's CORE engine logs (as opposed to provider logs)?
- [ ] `TF_LOG_PROVIDER`
- [ ] `TF_LOG_CORE`
- [ ] `TF_LOG_PATH`
- [ ] `TF_CORE_DEBUG`
<details>
<summary>Answer</summary>
✅ `TF_LOG_CORE`

Set to `TRACE` for the highest verbosity when preparing a bug report for the Terraform core team.
</details>

## Question 27
What is `terraform destroy` a convenience alias for?
- [ ] `terraform plan -destroy`
- [ ] `terraform apply -destroy`
- [ ] `terraform state rm --all`
- [ ] `terraform init -destroy`
<details>
<summary>Answer</summary>
✅ `terraform apply -destroy`

`terraform destroy` is shorthand for `terraform apply -destroy`; it takes no plan-file argument and always forces destroy mode.
</details>

## Question 28
How can you preview what a destroy operation would do WITHOUT executing it?
- [ ] `terraform destroy --dry-run`
- [ ] `terraform plan -destroy`
- [ ] `terraform show -destroy`
- [ ] There is no way to preview a destroy
<details>
<summary>Answer</summary>
✅ `terraform plan -destroy`

This runs `plan` in destroy mode, showing proposed destroy changes without executing them — a speculative destroy plan.
</details>

## Question 29
True or False: `terraform fmt` has customizable formatting rules/options for different team style preferences.
- [ ] True
- [ ] False
<details>
<summary>Answer</summary>
✅ False

`terraform fmt` is intentionally opinionated with NO customization options — the goal is cross-codebase consistency, not personal preference.
</details>

## Question 30
Which `terraform fmt` flag makes it act as a CI gate — exiting non-zero and listing files if any are improperly formatted, without writing changes?
- [ ] `-diff`
- [ ] `-recursive`
- [ ] `-check`
- [ ] `-list=false`
<details>
<summary>Answer</summary>
✅ `-check`

`-check` exits `0` if properly formatted, non-zero (with a list of bad files) otherwise — ideal for a CI formatting gate.
</details>
