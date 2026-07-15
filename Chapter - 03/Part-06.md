# Chapter 03 - Part 06: Destroying Infrastructure

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> `-destroy` planning mode itself already covered in Part 04/05 — this covers the `terraform destroy` command and the two ways to remove infrastructure.

---

## 1. Two Ways to Remove Infrastructure

| Approach | How | Effect |
|---|---|---|
| **Remove a single resource** | Delete (or comment out) its `resource` block in config, then `terraform apply` | Terraform destroys **only** that resource. Any output values referencing it must also be removed/commented, or the config becomes invalid. |
| **Destroy everything** | `terraform destroy` | Removes **all** resources managed by the workspace, leaving state empty. Typical for ephemeral/dev/test environments. |

---

## 2. `terraform destroy` — What It Actually Is

- **Not a separate engine** — it's a convenience alias for:
  ```
  terraform apply -destroy
  ```
- Accepts most `apply` options, **except**: it takes no plan-file argument, and it always forces **destroy** planning mode (can't be combined with other planning modes).
- `-destroy` as an `apply` flag only exists from **Terraform v0.15.2+**; on earlier versions, `terraform destroy` is the only way to get this behavior.

### Speculative Destroy Plan (preview only)
```
terraform plan -destroy
```
- Shows what **would** be destroyed, without executing — same idea as a normal speculative plan, just in destroy mode.

### Targeting a Specific Resource for Destroy
```
terraform destroy -target aws_instance.example
```
- Destroys just that resource **and its dependents** — same `-target` semantics covered in Part 04 (exceptional use only, not routine).

---

## Quick Recap (for revision)

- Removing a resource from config + `apply` = destroys just that resource (remember to clean up dependent outputs too).
- `terraform destroy` = shorthand for `terraform apply -destroy`; no plan-file arg; always destroy mode.
- `terraform plan -destroy` = preview-only destroy plan.
- `terraform destroy -target <addr>` = scoped destroy, same caveats as `-target` elsewhere.
