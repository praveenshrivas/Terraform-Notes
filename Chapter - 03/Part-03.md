# Chapter 03 - Part 03: `terraform validate` & Troubleshooting Terraform

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> Basic `fmt`/`validate` usage already covered in Part 01 — this covers the `validate` CLI reference and the troubleshooting model in depth.

---

## 1. `terraform validate` — Reference

- Checks configuration is **syntactically valid and internally consistent** — regardless of variables or existing state.
- Does **NOT** validate remote services (state backend, provider APIs) — that's what `plan` does (which includes an implied validation).
- Best used for verifying **reusable modules**; safe to run automatically (editor save-hook, CI test step).
- Requires an **initialized** working directory. To validate without touching a real backend:
  ```
  terraform init -backend=false
  ```

### Flags
| Flag | Effect |
|---|---|
| `-json` | Machine-readable JSON output (always disables color) — for editor/CI integrations |
| `-no-color` | Disable colored output |

### JSON Output — Key Fields
| Field | Meaning |
|---|---|
| `format_version` | Version of the JSON schema itself |
| `valid` | `true`/`false` overall result |
| `error_count` / `warning_count` | Counts (if `valid` is true, `error_count` is always 0) |
| `diagnostics[]` | Array of issues, each with `severity` (`error`/`warning`), `summary`, `detail`, `range` (file/position), `snippet` (code excerpt) |

---

## 2. The Terraform Troubleshooting Model — 4 Layers

Diagnose in this order (closest to you → furthest):

| Layer | What It Covers | Signs |
|---|---|---|
| **Language (HCL)** | Config syntax/semantics | `terraform fmt`/`validate` report line numbers + explanation |
| **State** | Mapping/metadata sync issues | Config looks fine but plan behaves unexpectedly — check via `refresh`/`import`/`-replace` (see Ch02 Part04) |
| **Core** | Terraform's own engine (graph building, state mgmt, plugin RPC) | Suspected bug — report to HashiCorp core repo |
| **Provider** | Plugin's auth/API/resource mapping logic | Suspected bug — report to that provider's repo |

---

## 3. Common Config Errors & Fixes

| Error | Cause | Fix |
|---|---|---|
| **Cycle error** (`Cycle: resourceA, resourceB`) | Two resources reference each other directly (e.g. two security groups each pointing at the other's ID in their own block) | Break the cycle: create the resources independently, then attach the relationship via a **separate** rule resource (e.g. `aws_security_group_rule`) referencing both IDs |
| **Invalid reference w/ splat (`*`) in `for_each`** | `for_each` requires a **map** (or set of strings); splat expressions (`resource.*.id`) produce a **list**, which `for_each` can't consume | Replace with a **local value** that builds a map, and reference `each.value` (not `each.id`) |
| **Missing resource instance key** (on outputs) | Once a resource has `for_each` (or `count`), a plain reference like `aws_instance.web_app.id` is ambiguous — output block doesn't know which instance | Use a **for expression** to collect the value across every instance: `[for instance in aws_instance.web_app : instance.id]` |

> `terraform validate` **stops at the first class of error it finds** — expect to fix and re-run several times as new errors surface underneath earlier ones.

---

## 4. Logging & Bug Reports

| Env Var | Purpose |
|---|---|
| `TF_LOG_CORE=TRACE` | Enables Terraform **core** engine logs (most verbose = `TRACE`) |
| `TF_LOG_PROVIDER=TRACE` | Enables **provider plugin** logs |
| `TF_LOG_PATH=<file>` | Writes enabled log streams to a file |

- Unset a variable (`export TF_LOG_CORE=`) to stop that log stream; all unset automatically when the terminal session closes.
- Before filing a bug: confirm versions with `terraform version` (shows Terraform + provider versions, flags if outdated), and check whether the issue is state/config vs. a genuine bug.
- Route the issue based on which log stream (`provider.terraform-provider-<name>` vs core) shows the root error — file against the Terraform core repo or the specific provider's repo accordingly.

---

## Quick Recap (for revision)

- `validate` = local syntax/consistency check only; needs `init` first; `-json` gives structured diagnostics with severity/summary/detail/range.
- Troubleshoot in order: **Language → State → Core → Provider**.
- Cycle errors = mutual resource references → decouple via a separate relationship resource.
- `for_each` needs a map, not a splat-produced list; once `for_each`/`count` is used, outputs need a `for` expression to reference all instances.
- `TF_LOG_CORE` / `TF_LOG_PROVIDER` / `TF_LOG_PATH` = structured debug logging for bug reports.
