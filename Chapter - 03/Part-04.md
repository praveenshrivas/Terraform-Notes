# Chapter 03 - Part 04: Dependency Graph & `terraform plan` Advanced Options

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> Basic `plan` usage (`-out`, `-destroy`, JSON plan fields) already covered in Part 01 — only new material here.

---

## 1. The Dependency Graph

Terraform builds a graph to drive plan/apply/refresh operations. (Advanced topic — not required for basic usage, but shows up on the exam.)

### Node Types
| Node | Represents |
|---|---|
| **Resource Node** | One resource. If `count` is set, one node **per count index**. |
| **Provider Configuration Node** | The moment a provider is fully configured (e.g. AWS credentials applied) |
| **Resource Meta-Node** | A convenience grouping of resources sharing a `count > 1`; represents no action itself |

- `terraform graph` visualizes all of these.

### Building the Graph (sequence)
1. Add resource nodes from config (attach diff/state metadata if present)
2. Map resources to their provisioners
3. Add edges from explicit `depends_on`
4. Add any **orphan** resources (in state but no longer in config — no config attached)
5. Map resources to providers → provider config nodes → edges (resource depends on provider being configured)
6. Parse interpolations/references → create dependency edges between resources
7. Create a single **root node** pointing at everything (ignored during traversal)
8. If a diff exists, **split** any resource being destroyed-and-recreated into two nodes (destroy node + create node) — because destroy order often differs from create order
9. Validate: **no cycles**, **single root**

### Walking the Graph
- **Depth-first traversal**, walked **in parallel** — a node runs as soon as all its dependencies have completed.
- Parallelism is capped by a semaphore — **default 10** concurrent nodes.
- Controlled via `-parallelism=n` on `plan`/`apply`/`destroy` — considered an **advanced/rarely-needed** option (debugging or special cases only).
- Note: Terraform's `-parallelism` does **not** address provider API rate limits directly — providers like AWS handle that themselves via backoff/retry in their API clients.

---

## 2. `terraform plan` — Planning Modes

Mutually exclusive; both also apply to `terraform apply`.

| Mode | Flag | Purpose |
|---|---|---|
| **Normal** (default) | *(none)* | Reads state → diffs vs. config → proposes changes to match config |
| **Destroy mode** | `-destroy` | Plans to destroy all managed objects (same as `terraform destroy`) |
| **Refresh-only mode** | `-refresh-only` | Plans **only** to update state/output values to match out-of-band changes to real infra — doesn't touch resource config. (v0.15.4+) |

> In v0.15 and earlier, `-destroy` worked only with `terraform plan`, not `apply` — you had to use `terraform destroy` directly.

---

## 3. `terraform plan` — Planning Options

| Option | Effect |
|---|---|
| `-refresh=false` | Skips syncing state with real infra before planning — faster, but may miss external drift. **Cannot combine with `-refresh-only`.** |
| `-replace=ADDRESS` | Marks a specific resource instance for replacement even if config implies only an update/no-op — repeatable for multiple resources. Cannot combine with `-destroy`. (v0.15.2+; `taint` was the old equivalent.) |
| `-target=ADDRESS` | Scopes planning to specific resource(s) + their dependencies only — for exceptional recovery/debugging, **not routine use** (risks undetected drift) |
| `-var 'NAME=VALUE'` | Sets one input variable value inline; repeatable |
| `-var-file=FILENAME` | Loads variable values from a `.tfvars` file; repeatable; generally **preferred over `-var`** for complex values |

### `-var` Syntax Notes
- No spaces around `=`.
- Unix shells: wrap in single quotes — `-var 'name=value'`.
- Windows: use **Command Prompt** (not PowerShell, which can't pass literal quotes) — double quotes: `-var "name=value"`.
- Complex types (list/map/set) need a full Terraform expression as the value: `-var 'name=["a","b","c"]'`.

### `-target` Address Resolution
- A specific instance address (e.g. `aws_instance.example[0]`) → selects just that instance.
- A bare resource address → selects **all** instances of it (all `count`/`for_each` indices).
- A module address → selects all resources in that module **and its children**.
- Always auto-extends to include anything the targeted resource(s) depend on.
- **Recommended alternative** to routine `-target` use: split large configs into smaller, independently-applied configurations, using data sources to reference resources from other configs.

---

## 4. `terraform plan` — Other Options

| Option | Effect |
|---|---|
| `-compact-warnings` | Shows only warning summaries (unless a warning accompanies an error, where full text may still be useful) |
| `-detailed-exitcode` | Changes exit codes: `0` = no changes, `1` = error, `2` = changes present — useful for CI branching logic |
| `-generate-config-out=PATH` | *(Experimental)* Generates HCL for resources referenced by `import` blocks that aren't yet in config; PATH must not already exist |
| `-input=false` | Don't prompt for unassigned variables — for non-interactive automation |
| `-json` | Machine-readable output; implies `-input=false`, so all variables must already be assigned |
| `-lock=false` / `-lock-timeout=DURATION` | Same locking controls as `init` (see Ch02/Ch03) |
| `-no-color` | Disable colored output |
| `-parallelism=n` | See graph-walking section above (default 10) |

> `-chdir` (global) replaces the deprecated positional-directory argument for overriding the root module directory — same as covered for `init` in Part 02.

---

## Quick Recap (for revision)

- Graph nodes: **Resource**, **Provider Config**, **Resource Meta-Node** (for `count`); built in a fixed sequence ending in cycle/single-root validation.
- Graph walk = parallel depth-first, capped at **10 concurrent** by default (`-parallelism`).
- Planning modes: **Normal** / **`-destroy`** / **`-refresh-only`** — mutually exclusive.
- `-refresh=false` skips drift detection; `-replace` forces recreation; `-target` scopes to specific resources (exceptional use only).
- `-var`/`-var-file` set input variables from the CLI; prefer `-var-file` for complex values.
- `-detailed-exitcode` (0/1/2) is the key flag for CI "were there changes?" logic.
