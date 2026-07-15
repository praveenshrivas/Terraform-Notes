# Chapter 03 - Part 07: `terraform fmt` — CLI Reference

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> Basic `fmt` usage and the troubleshooting model were already covered in Parts 01 and 03 — this is the full CLI flag reference only.

---

## 1. What It Does

- Rewrites config files to match Terraform's **canonical format/style** — a subset of the language style guide plus minor readability tweaks.
- **Opinionated by design, no customization options** — the goal is cross-codebase consistency, not everyone's personal preference.
- Terraform-generated config (from commands that produce `.tf` files) already conforms to this style.
- Canonical format can shift slightly between Terraform **minor versions** — re-run `fmt` after upgrading. New formatting rules are **not** considered breaking changes.
- If you dislike its choices, you can skip it and use a third-party formatter instead — but then also run that tool on any Terraform-auto-generated files for consistency.

---

## 2. Usage

```
terraform fmt [options] [target...]
```

- No target → scans the **current directory**.
- Target can be: a directory, a specific file, or **`-`** (stdin).

### Flags
| Flag | Effect |
|---|---|
| `-list=false` | Suppress the list of files with formatting inconsistencies (listing is the default) |
| `-diff` | Show the actual diff of formatting changes |
| `-write=false` | Don't overwrite files (default behavior when using `-check` or reading from stdin) |
| `-check` | **Check-only mode**: exits `0` if already formatted, non-zero + lists bad files otherwise — doesn't write changes. Useful as a CI gate. |
| `-no-color` | Disable colored output |
| `-recursive` | Also process subdirectories (default is current/target directory only, non-recursive) |

---

## Quick Recap (for revision)

- `fmt` is opinionated and unconfigurable — consistency over preference.
- `-check` = CI-friendly "is this formatted?" gate (non-zero exit + file list if not).
- `-recursive` = needed to format subdirectories; default is non-recursive.
- `-diff` = preview the actual formatting changes; `-write=false` = don't apply them.
