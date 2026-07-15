# Chapter 02 - Part 02: How Terraform Works with Plugins

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> All other content in this batch (provider block, `required_providers`, lock file, `terraform init` steps) was already covered in [Part 01](./02-providers-and-lock-file.md) — not repeated here.

---

## 1. Two-Part Architecture

Terraform is split into two logical parts that talk over **RPC (remote procedure calls)**:

| Component | What It Is | Responsibilities |
|---|---|---|
| **Terraform Core** | Statically-compiled Go binary (the `terraform` CLI) | Read/interpolate config & modules • manage resource **state** • build the **resource graph** • execute **plans** • communicate with plugins over RPC |
| **Terraform Plugins** | Separate executable binaries (also Go), invoked by Core over RPC | Providers and Provisioners — each implements one service (e.g. AWS) or provisioner (e.g. `bash`) |

- Plugins run as **separate processes** from Core.
- Core abstracts away plugin discovery/RPC details — plugin developers don't manage that.

### Provider Plugin responsibilities
- Initialize any libraries needed for API calls
- Authenticate with the infrastructure platform
- Define managed **resources** and **data sources**
- Define functions supporting practitioner configuration logic

### Provisioner Plugin responsibilities
- Execute commands/scripts on a resource **after creation** or **on destroy**
- Several provisioners are **built into Terraform Core**; providers, by contrast, are always **discovered dynamically**

---

## 2. Plugin Discovery & Selection (during `terraform init`)

When `terraform init` runs, Terraform:
1. Reads config to determine required plugins
2. Searches known plugin locations for installed versions
3. Downloads additional plugins if needed
4. Selects a version per plugin
5. Writes a lock file so the same versions are reused until the next `init`

**Version selection logic:**
| Situation | Terraform's Choice |
|---|---|
| An acceptable version is already installed | Uses the **newest installed version** meeting the constraint — even if a newer one exists on the Registry |
| No acceptable version installed, provider is on the Terraform Registry | Downloads newest acceptable version into `.terraform/providers/` |
| No acceptable version installed, provider is **not** on the Registry | `init` **fails** — must be installed manually |

**Upgrading (`terraform init -upgrade`):**
- Re-checks the Registry for newer acceptable versions and downloads them.
- Only affects providers whose acceptable versions live in the `.terraform/providers/` auto-download directory — if a valid version exists elsewhere (e.g. a local/filesystem mirror), `-upgrade` won't touch it.

---

## Quick Recap (for revision)

- **Terraform Core** = state, graph, plan/apply logic, RPC client. **Plugins** = providers/provisioners, separate processes.
- Provisioners can be built-in; **providers are always discovered dynamically**.
- Plugin selection prefers the newest **already-installed** compatible version over checking the Registry — `-upgrade` forces a fresh Registry check (but only for auto-downloaded providers).
