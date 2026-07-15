# Chapter 03 - Part 01: Core Workflow in Practice — Write, Plan, Apply

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> `terraform init` mechanics and resource-block syntax already covered in Chapter 02 — not repeated here.

---

## 1. The Core Workflow at Different Scales

| Scale | Write | Plan | Apply |
|---|---|---|---|
| **Individual** | Edit config locally; `terraform plan` in a tight local feedback loop to catch syntax errors early | `terraform apply` shows the plan for final review before confirming | Type `yes` to provision; push repo to remote afterward |
| **Team** | Work in **version control branches** to avoid collisions; sensitive inputs (API keys, certs) make local-only runs harder to scale, so teams often move Terraform execution into **CI** | Plan output reviewed via **pull requests** — lets teammates evaluate intent/risk before merging; speculative plans posted to PRs (manually or by CI) | After merge, the **final concrete plan** (against latest state) is reviewed again — it can differ from the PR's plan due to merge order or infra drift. Team discusses risk/disruption before applying. |
| **HCP Terraform (enhanced)** | Centralizes **input variables** and **state**; restores a tight feedback loop via CLI integration (`cloud` block) — `terraform plan` runs remotely | Auto-runs a **speculative plan** on PR creation with a status check; full plan detail viewable in HCP Terraform UI | Presents the concrete plan for team approval; shows live apply progress to watchers |

- Key pattern across all scales: it's a **loop** — write → plan → apply → repeat.

---

## 2. `terraform plan` — What It Does & Options

- Compares **configuration** vs. **state** (refreshing first) to build an **execution plan**: a set of create/update/destroy actions.
- Does **not** change infrastructure — only `apply` does.
- `-out <file>` saves the plan to a binary file — used in CI/CD to guarantee **exactly** the reviewed changes are applied later, even across machines/times.
- `-destroy` generates a plan to **destroy** all managed resources (this is what `terraform destroy` runs under the hood).
- `terraform show <planfile>` — prints a saved plan in human-readable form (or use `-json` for machine-readable output, e.g. for CI tooling or Sentinel policy checks).

### Saved Plan File (JSON) — Key Fields (exam-relevant)

| Field | Contains |
|---|---|
| `terraform_version` / `format_version` | Versions used to generate the plan — ensures same version applies it |
| `configuration` | Snapshot of config at plan time (provider versions, resources, module calls, references) |
| `variables` | Input variable values used — **note:** sensitive variables are still stored in **plaintext** in the plan file |
| `prior_state` | State exactly as it was before this plan (only present if state already existed) |
| `resource_changes` | Per-resource `actions` (`create`/`update`/`delete`), plus `before`/`after`/`after_unknown`/`sensitive` value sets |
| `planned_values` | The resulting expected state if applied — used by **Sentinel** and **cost estimation** |

> ⚠️ **Never commit plan files to version control** (binary or JSON) — they can contain sensitive data.

---

## 3. `terraform apply` — What It Does

Sequence when you approve an apply:
1. **Locks** workspace state (prevents concurrent runs — errors if a lock file already exists)
2. Creates a plan and waits for approval (or executes an already-**saved** plan without prompting — used in automation)
3. Executes the plan's steps via installed providers — **parallel** where possible, **sequential** where resources depend on each other
4. **Updates state** with the new resource snapshot
5. **Unlocks** state
6. Reports changes + outputs

### Error Handling During Apply
On error, Terraform: logs/reports it → updates state with whatever succeeded → unlocks state → exits.
- **No automatic rollback** of a partially-completed apply — you must fix the issue and re-apply.
- Common causes: out-of-band infra changes, transient network errors, provider API errors (duplicate name, rate limit), provider/Terraform bugs.
- If a resource was changed/deleted outside Terraform, the next `plan`/`apply` auto-refreshes state and reports **"Objects have changed outside of Terraform"** before proposing a reconciling plan.

### Targeting Individual Resources

| Flag | Use Case |
|---|---|
| `-replace "<resource address>"` | Force destroy + recreate one unhealthy resource with no config change (e.g. bad OS state) |
| `-target` | Apply/plan against specific resource(s) only — mainly for troubleshooting a blocked full apply |

---

## 4. Formatting & Validating Config

| Command | Purpose |
|---|---|
| `terraform fmt` | Auto-reformats `.tf` files to HashiCorp's canonical style; prints names of files it changed |
| `terraform validate` | Checks syntax validity + internal consistency (e.g. missing/invalid resource arguments). Requires `init` to have run first. |

---

## Quick Recap (for revision)

- Same Write→Plan→Apply loop scales from individual → team (PR review) → HCP Terraform (automated speculative plans + centralized state/vars).
- `terraform plan -out` = reproducible saved plan for CI; plan JSON has `configuration`, `resource_changes`, `planned_values` — sensitive vars still stored in plaintext.
- `apply`: lock → plan/confirm → execute (parallel where possible) → update state → unlock. No auto-rollback on error.
- `-replace` = recreate one resource; `-target` = scope an operation to specific resources.
- `terraform fmt` = style; `terraform validate` = syntax/consistency check (needs `init` first).
