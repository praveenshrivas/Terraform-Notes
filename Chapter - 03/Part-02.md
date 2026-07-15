# Chapter 03 - Part 02: `terraform init` — CLI Flag Reference

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> Dependency lock file mechanics and the general `init` workflow were already covered in Chapter 02 — only new CLI-flag details here.

---

## 1. General Notes

- `terraform init` is **always safe to run multiple times** — never deletes existing config or state, though it may error if things are out of sync.
- First command to run after writing new config or cloning an existing one.

---

## 2. General Flags

| Flag | Effect |
|---|---|
| `-input=true` | Prompts for input if needed; `false` errors instead if input is required |
| `-lock=false` | Disables state locking during state-related operations |
| `-lock-timeout=<duration>` | How long to wait for a state lock (default `0s` = fail immediately if locked) |
| `-no-color` | Disables colored output |
| `-upgrade` | Upgrades modules **and** plugins to newest versions matching constraints (ignores lock file selections) |
| `-json` | Machine-readable JSON UI output |

---

## 3. Copying a Source Module — `-from-module`

- Run against an **empty directory**: `terraform init -from-module=<SOURCE>` copies a module in before other init steps run.
- Use cases: shorthand for checking out config from VCS + initializing in one step, or bootstrapping from an example config.
- **Not recommended for routine use** — prefer checking out via your VCS directly so you retain full VCS flags/control.

---

## 4. Backend Initialization Flags

| Flag | Effect |
|---|---|
| `-reconfigure` | Disregards existing backend config; does **not** migrate state |
| `-migrate-state` | Attempts to copy existing state to a new backend (may prompt interactively) |
| `-force-copy` | Suppresses migration prompts, auto-answers "yes"; also implies `-migrate-state` |
| `-backend=false` | Skips backend configuration entirely (only when working dir was already initialized for that backend) |
| `-backend-config=...` | Supplies partial backend settings — for dynamic/sensitive values that shouldn't be hardcoded in config |

> Re-running `init` with an already-initialized backend requires **`-reconfigure`** or **`-migrate-state`** to actually change backend settings.

---

## 5. Child Module Installation Flags

- By default, re-running `init` installs sources only for **newly added** modules — existing ones are left alone.
- `-upgrade` overrides this, updating **all** modules to latest matching source.
- `-get=false` — skip module installation (only when modules were already installed previously).

---

## 6. Plugin (Provider) Installation Flags

| Flag | Effect |
|---|---|
| `-upgrade` | Upgrades all previously-selected providers to newest version matching constraints; ignores lock file |
| `-plugin-dir=PATH` | Forces provider installation to read **only** from this directory (like a one-off filesystem mirror) — useful for testing a local provider build |
| `-lockfile=readonly` | Suppresses lock file changes but still verifies checksums against what's recorded; **conflicts with `-upgrade`**; useful when a third-party tool manages the lock file |
| `-get-plugins=false` | **Removed in Terraform 0.15+** — superseded by `provider_installation` / `plugin_cache_dir` CLI settings |

---

## 7. Working Directory Override

- `-chdir=<DIR>` (global option, not init-specific) — makes Terraform treat `<DIR>` as the working directory for **all** file reads/writes, across any command. Replaces the deprecated (and since v0.15, removed) practice of passing a directory path directly to `terraform apply`.
- `TF_DATA_DIR` (env var) — redirects where the `.terraform` subdirectory is written, useful if `-chdir` points elsewhere but you still want `.terraform` in the original location.

---

## Quick Recap (for revision)

- `init` is idempotent/always safe to re-run.
- `-upgrade` = ignore lock file, get newest matching versions (modules **and** plugins).
- Backend changes need `-reconfigure` (no migrate) or `-migrate-state` (copies state); `-force-copy` auto-confirms.
- `-lockfile=readonly` verifies without writing; conflicts with `-upgrade`.
- `-chdir` = global working-directory override; `TF_DATA_DIR` = relocate just the `.terraform` cache dir.
