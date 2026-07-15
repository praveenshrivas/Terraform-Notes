# Chapter 02 - Part 03: Defining Infrastructure with Resources

> HashiCorp Certified: Terraform Associate (004) — Study Notes
> Provider block / provider requirements content in this batch was already covered in Part 01 — not repeated here.

---

## 1. Resource Blocks

- A **resource block** declares one or more infrastructure objects (networks, compute instances, DNS records, etc.).
- Syntax: `resource "<TYPE>" "<NAME>" { ... }`
- **Resource type** always starts with the **provider's prefix** — e.g. `aws_instance`, `aws_security_group` belong to the `aws` provider; `random_pet` belongs to the `random` provider.

```hcl
resource "random_pet" "name" {}

resource "aws_instance" "web" {
  ami           = "ami-a0cfeed8"
  instance_type = "t2.micro"
  tags = {
    Name = random_pet.name.id
  }
}
```

### Resource ID
- **Type + Name** together form the resource's identifier: `resource_type.resource_name` (e.g. `random_pet.name`).
- Must be **unique within a workspace**. Terraform uses this ID in all output/state references.

---

## 2. Arguments vs. Attributes vs. Meta-Arguments

| Concept | What It Is | Notes |
|---|---|---|
| **Arguments** | Inputs that configure the resource | Resource-specific; can be required or optional (provider-defined). Missing a required argument = apply error. |
| **Attributes** | Values **exposed by** an existing resource | Often assigned by the cloud provider/API itself, not set by you. Referenced as `resource_type.resource_name.attribute_name`. |
| **Meta-arguments** | Change a resource's *behavior* (e.g. `count`) | A function of **Terraform itself** — not resource- or provider-specific. |

---

## 3. Implicit Dependencies

Referencing one resource's attribute inside another resource's argument creates an **implicit dependency** — Terraform automatically sequences creation accordingly.

```hcl
tags = {
  Name = random_pet.name.id   # aws_instance now implicitly depends on random_pet.name
}
```
- Terraform won't create `aws_instance.web` until `random_pet.name` exists, since it needs the `.id` value.
- This is how Terraform builds part of its **resource graph** without you declaring dependencies manually.

---

## 4. Finding Resource Documentation

- The **Terraform Registry** hosts documentation for every provider's resources — arguments, attributes, and usage examples — nested under that provider's page (e.g. `aws_security_group` docs live under the `aws` provider).

---

## Quick Recap (for revision)

- Resource block = `resource "<type>" "<name>" {}`; type prefix = provider name.
- Resource ID = `type.name`, must be unique per workspace.
- **Arguments** (you set) vs **Attributes** (resource exposes) vs **Meta-arguments** (Terraform-level, e.g. `count`).
- Referencing another resource's attribute = **implicit dependency**, feeds the resource graph.
