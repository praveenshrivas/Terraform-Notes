# Chapter 03 - Part 05: `terraform apply` — CLI Reference

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> The apply execution sequence (lock → plan → execute → update state → unlock), error handling, and `-replace`/`-target` concepts were already covered in Part 01 — this is the CLI flag/mode reference only.

---

## 1. Two Modes of Operation

| Mode | How | Behavior |
|---|---|---|
| **Automatic Plan Mode** | `terraform apply` (no plan file arg) | Generates a plan **as if running `terraform plan`**, prompts for approval, then executes. All planning modes/options (`-destroy`, `-refresh-only`, `-replace`, `-var`, etc.) are available here. |
| **Saved Plan Mode** | `terraform apply <planfile>` | Executes **exactly** what's in the saved plan — **no confirmation prompt** (passing the file *is* the approval). You **cannot** add planning modes/options here — the plan file already locked in those decisions. Use `terraform show <planfile>` to inspect it first. |

- `-auto-approve` skips the interactive confirmation in **Automatic Plan Mode** only — Terraform ignores it when a saved plan file is passed (redundant, since passing the file is already the approval).
  - ⚠️ If using `-auto-approve` routinely, make sure nothing else can change your infra outside Terraform — reduces drift risk.

---

## 2. Apply-Specific Flags

| Flag | Effect |
|---|---|
| `-auto-approve` | Skip interactive approval (Automatic Plan Mode only) |
| `-compact-warnings` | Same as `plan` — condensed warning output |
| `-input=false` | Disables **all** interactive prompts — including plan approval, so apply will **fail** if it would otherwise need to prompt. Use proper automation patterns instead (see Running Terraform in Automation). |
| `-json` | Machine-readable output. Implies `-input=false`. **Requires** either `-auto-approve` **or** a saved plan file — JSON mode can't sit and wait for an interactive yes/no. |
| `-lock=false` / `-lock-timeout=DURATION` | State locking controls (same as `init`/`plan`) |
| `-no-color` | Disable colored output |
| `-parallelism=n` | Cap concurrent graph operations (default 10) |
| `-replace=<resource address>` | Force-replace a specific resource instance (Automatic Plan Mode only, since Saved Plan Mode can't take extra planning options) |

- **All `plan` planning modes/options** (`-destroy`, `-refresh-only`, `-var`, `-var-file`, `-target`, etc.) are available on `apply` — but **only** in Automatic Plan Mode (no saved plan file).
- Legacy, **local-backend-only** flags: `-state`, `-state-out`, `-backup`.

---

## 3. Directory Override

- Same as `plan`/`init`: the old positional-directory argument to `apply` was deprecated in v0.14 and **removed in v0.15**.
- Use the global `-chdir=<DIR>` option instead (applies across all commands).
- Use `TF_DATA_DIR` if you need `.terraform` written somewhere other than the `-chdir` target.

---

## Quick Recap (for revision)

- **Automatic Plan Mode** = plans + prompts (unless `-auto-approve`); accepts all `plan` flags. **Saved Plan Mode** = executes a pre-approved plan file exactly, no prompt, no extra flags.
- `-json` on apply needs `-auto-approve` or a saved plan — can't combine with an interactive wait.
- `-input=false` disables prompting entirely, including approval — will fail if approval would've been needed.
- `-replace` works in Automatic Plan Mode only.
- `-chdir` (global) is the modern way to point Terraform at a different working directory; `TF_DATA_DIR` relocates just the `.terraform` cache.
