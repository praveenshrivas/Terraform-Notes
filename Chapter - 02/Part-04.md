# Chapter 02 - Part 04: Terraform State

> HashiCorp Certified: Terraform Associate (004) — Study Notes

---

## 1. Why State Is Required

| Purpose | Explanation |
|---|---|
| **Mapping to the real world** | State binds each config resource (e.g. `aws_instance.foo`) to a real remote object ID (e.g. `i-abcd1234`). Early Terraform tried tags instead — failed since not all resources/providers support tags. |
| **One-to-one mapping** | Each remote object must map to exactly **one** resource instance in config; ambiguous mapping = unexpected behavior. Enforced automatically on create; must be manually ensured when importing. |
| **Metadata / dependency tracking** | State retains the last-known dependency graph. Needed because deleting a resource from config removes Terraform's only other way to know deletion order. |
| **Performance (optional)** | State caches resource attribute values so `plan` doesn't need to re-query every resource. For large infra, full sync is slow (API rate limits) — teams use `-refresh=false` / `-target` and treat cached state as truth. |
| **Syncing (teams)** | Default state = local file. For teams, use **remote state** — enables **remote locking** to prevent concurrent runs and keeps everyone working off the same state. |

---

## 2. State File Basics

- Default location: `terraform.tfstate` in the working directory — JSON encoded.
- **Never manually edit the state file** — risks drift and unintended destroy/recreate.
- Each resource entry has: `mode` (`managed` = resource, `data` = data source), `type`, `name`, `provider`, `instances` (attributes), and `dependencies`.

---

## 3. Inspecting State (CLI — don't touch the file directly)

| Command | Purpose |
|---|---|
| `terraform show` | Human-readable dump of all resources/outputs currently in state |
| `terraform state list` | Lists resource addresses in state (useful to locate a specific resource) |

---

## 4. Modifying State Safely

| Command / Block | Use Case | Notes |
|---|---|---|
| `terraform plan/apply -replace="<addr>"` | Force destroy + recreate a specific resource | Replaces the deprecated `terraform taint`. Introduced in 0.15.2. |
| `terraform state mv` | Move/rename a resource between state files, or rename within one | Only updates **state**, not config. Target name must be unique in destination. |
| `removed` block (Terraform 1.7+) | Stop managing a resource **without destroying it** | Set `lifecycle { destroy = false }`; also delete/comment the matching `resource` block. Replaces old `terraform state rm`. |
| `terraform import <addr> <id>` | Bring an existing real-world object under Terraform management | Requires a matching `resource` block already in config. |
| `terraform refresh` | Sync state with real infrastructure changed **outside** Terraform | Doesn't touch config — follow with `terraform plan` to see the resulting diff. Runs automatically as part of `plan`/`apply`/`destroy`. |

---

## Quick Recap (for revision)

- State = mapping (config ↔ real resource) + dependency metadata + optional attribute cache.
- Remote state + locking = how teams stay in sync.
- Never hand-edit `.tfstate` — use CLI: `show`, `state list`, `-replace`, `state mv`, `removed` block, `import`, `refresh`.
- `-replace` replaced `taint`; `removed` block replaced `state rm`.
