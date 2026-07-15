# Chapter 02 - Part 01: Providers & Dependency Lock File

> HashiCorp Certified: Terraform Associate (004) — Study Notes

---

## 1. What Providers Do

- **Providers** are plugins that let Terraform manage resources on a platform (cloud, SaaS, or local utility) via its API.
- Each provider adds **resource types** and/or **data sources**. No providers = Terraform can't manage anything.
- Distributed **separately** from Terraform core, each with its own release cadence/versioning.
- Public source: **Terraform Registry**.

### Provider Tiers (Registry badges)

| Tier | Maintained By |
|---|---|
| **Official** | HashiCorp itself |
| **Partner Premier** | Vetted tech partners (meets extra requirements) |
| **Partner** | Third-party companies via HashiCorp Technology Partner Program |
| **Community** | Individual/community maintainers |
| **Archived** | No longer maintained (Official or Partner) |

---

## 2. Declaring Providers — `required_providers`

Every root module must declare the providers it needs, inside the `terraform` block:

```hcl
terraform {
  required_providers {
    mycloud = {
      source  = "mycorp/mycloud"
      version = "~> 1.0"
    }
  }
}
```

- **Local name** (the key, e.g. `mycloud`) — module-specific, unique per module, used everywhere else in the config. Prefer the provider's **preferred local name** (usually matches its resource-type prefix, e.g. `aws_instance`).
- **Source address** — global identifier: `[<HOSTNAME>/]<NAMESPACE>/<TYPE>`. Hostname defaults to `registry.terraform.io` if omitted.
  - If `source` is omitted entirely, Terraform implies `registry.terraform.io/hashicorp/<local name>`.
- **Local name conflicts**: if two providers share a preferred local name, use a compound name (`hashicorp-http`, `mycorp-http`) and set `provider = <name>` explicitly on affected resources/data sources.

### Version Constraints — Best Practice

- Always set a **minimum** version for reusable/child modules: `version = ">= 1.0"`
- Set a **minimum + maximum** for root modules to avoid accidental upgrades: `version = "~> 1.0.4"` (`~>` locks all but the rightmost version segment)
- Omitting `version` = any version accepted (not recommended).

### Built-in Provider

- One provider — `terraform.io/builtin/terraform` — ships **inside** Terraform itself (powers `terraform_remote_state`). No `required_providers` entry needed.
- Legacy `hashicorp/terraform` is a different, incompatible provider — never declare it.

---

## 3. Configuring a Provider — `provider` Block

```hcl
provider "google" {
  project = "acme-app"
  region  = "us-central1"
}
```

- Belongs in the **root module only**; child modules inherit provider config from the parent (don't define `provider` blocks in child modules).
- Body arguments are provider-specific (defined by the provider itself).
- No block written → Terraform assumes an empty default config (errors if the provider has required arguments).

### `alias` — Multiple Configs for One Provider

```hcl
provider "aws" { region = "us-east-1" }                  # default
provider "aws" { alias = "west"; region = "us-west-2" }  # aliased
```
- Reference via `provider = aws.west` on a resource/data/module block.
- The un-aliased block = default config. If *all* blocks are aliased, Terraform creates an implied empty default.
- Child modules need `configuration_aliases` in their own `required_providers` to accept a passed-in aliased provider.
- `version` argument inside `provider {}` is **deprecated** — use `required_providers` instead.

---

## 4. Dependency Lock File (`.terraform.lock.hcl`)

- Tracks **provider** version selections only (not remote module versions — those always resolve to newest matching version).
- One file per configuration (root working directory), auto-created/updated by `terraform init`.
- **Commit it to version control.**
- Records per provider: `version`, `constraints` (informational only), and `hashes` (checksums — `zh:` legacy zip-hash, `h1:` preferred content hash).

### Installation Behavior (`terraform init`)

| Scenario | Result |
|---|---|
| No lock file | Installs **newest version** matching `required_providers` constraint; writes lock file |
| Lock file exists | **Always reuses the locked version**, even if newer versions exist |
| `terraform init -upgrade` | Ignores existing lock selections → picks newest matching version, updates lock file |
| Provider no longer used (removed from config + state) | `terraform init` removes its lock entry |

- Checksum verification = "trust on first use": Terraform errors if an installed package doesn't match a recorded hash.
- Direct manual edits to the lock file are discouraged — let `terraform init` manage it.

---

## 5. `terraform init` — What It Actually Does

Run whenever you: write new config, clone a repo, or change providers/modules/backend.

1. Initializes the **backend**
2. Downloads/installs **modules** (local modules referenced directly; remote modules downloaded to `.terraform/modules`)
3. Downloads/installs **providers** into `.terraform/providers` (per lock file or `required_providers`)
4. Creates/updates the **lock file**

Also supports:
- **Plugin cache** (`plugin_cache_dir` in CLI config) — saves bandwidth across projects
- `.terraform` directory — auto-managed cache of providers/modules; **never commit or hand-edit it**
- `terraform validate` — checks config syntax/consistency; requires `init` to have run first

---

## Quick Recap (for revision)

- Provider = plugin; declared via `required_providers` (local name + source + version); configured via `provider {}` block.
- Prefer min-version for modules, min+max (`~>`) for root configs.
- Lock file = locks provider versions across a team; `-upgrade` overrides it.
- `terraform init`: backend → modules → providers → lock file.
