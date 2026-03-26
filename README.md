# Terraform Provider Google Workspace

[![Releases](https://img.shields.io/github/release/arslanbekov/terraform-provider-googleworkspace.svg)](https://github.com/arslanbekov/terraform-provider-googleworkspace/releases)
[![LICENSE](https://img.shields.io/github/license/arslanbekov/terraform-provider-googleworkspace.svg)](https://github.com/arslanbekov/terraform-provider-googleworkspace/blob/main/LICENSE)

> **Why this fork?** Google does not maintain an official Terraform provider for Google Workspace.
> The only one that existed was built by HashiCorp, abandoned in 2022, and [archived in June 2025](https://github.com/hashicorp/terraform-provider-googleworkspace).
> Community forks (SamuZad, Vandebron) kept it alive but didn't fix critical bugs.
> This fork fixes the bugs, updates dependencies, and keeps the provider usable.

This provider allows you to manage domains, users, groups, and organizational units in your Google Workspace via Terraform.

## What's different from upstream

**Bug fixes:**
- **Fix custom_schemas permadiff** ([upstream #121](https://github.com/hashicorp/terraform-provider-googleworkspace/issues/121)) — `custom_schemas` blocks showed perpetual diffs on every plan because the Google API returns schemas as an unordered map, but the provider compared them positionally. Fixed by sorting schemas by name.
- **Fix credential leak in error messages** — validation errors previously included the full service account JSON (with private key) in the error output. Now only the error reason is shown.
- **Fix panic in DiffSuppressFunc** — a `json.Marshal` failure in `custom_schemas` diff suppression crashed the entire Terraform process. Now returns safely.
- **Fix eventual consistency check** — `reachedConsistency` used strict equality (`==`) instead of `>=`, causing false negatives.

**Modernization:**
- **Go 1.26**, latest `google.golang.org/api`, `terraform-plugin-sdk` v2.40, and all transitive deps.
- **Go vet compliance** — fixed non-constant format strings required by Go 1.25+.
- **GitHub Actions** updated to latest versions (checkout v6, setup-go v6, goreleaser-action v7).

## Requirements

- [Terraform](https://www.terraform.io/downloads.html) >= 1.0
- [Go](https://golang.org/doc/install) >= 1.26 (for building)

## Usage

```hcl
terraform {
  required_providers {
    googleworkspace = {
      source  = "arslanbekov/googleworkspace"
      version = "~> 1.0.0"
    }
  }
}

provider "googleworkspace" {
  customer_id = "C01xxxxxx"
}
```

## Upgrading

```bash
terraform init -upgrade
```

## Building

```sh
git clone https://github.com/arslanbekov/terraform-provider-googleworkspace.git
cd terraform-provider-googleworkspace
make build
```

## Documentation

See the [provider documentation](https://registry.terraform.io/providers/arslanbekov/googleworkspace/latest/docs) for resource and data source reference.

## Credits

- [HashiCorp](https://www.hashicorp.com/) — original provider
- [Chase (DeviaVir)](https://github.com/DeviaVir) — `terraform-provider-gsuite`, the inspiration for the original project
- [SamuZad](https://github.com/SamuZad) — earlier community fork
