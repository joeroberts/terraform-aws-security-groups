# Repository Guidelines

## Project Structure & Module Organization

The root Terraform files (`main.tf`, `variables.tf`, `outputs.tf`, and
`versions.tf`) implement the general AWS security-group module. Preset modules
live under `modules/<service>/`; matching `for_each` wrappers live under
`wrappers/<service>/`. `generate/catalog.tf` and `generate/templates/` are the
source of truth for generated presets. The root module requires Terraform
`>= 1.5.7` and `hashicorp/aws >= 6.29`. Use `examples/complete/` as the
integration example. CI workflows are in `.github/workflows/`; upgrade and
provenance notes are in `docs/` and `UPSTREAM.md`.

## Build, Test, and Development Commands

Run commands from the repository root unless noted; there is no separate build
step:

- `terraform fmt -check -recursive` checks canonical HCL formatting.
- `terraform init -backend=false && terraform validate` initializes providers
  without a backend and validates the root module.
- `pre-commit run --all-files` runs formatting, documentation, TFLint, wrapper,
  and validation hooks used by CI.
- `terraform -chdir=generate init -input=false` prepares the local generator.
- `terraform -chdir=generate apply -auto-approve -input=false` regenerates
  `modules/<service>/`; inspect the resulting diff before committing it.

## Coding Style & Naming Conventions

Follow `.editorconfig`: UTF-8, LF, final newlines, and two-space Terraform
indentation. Let `terraform fmt` determine HCL layout. Use
`snake_case` for variables, outputs, locals, and resource labels. Keep service
directory names aligned with keys in `generate/catalog.tf`. Add descriptions
to public variables and outputs, and explicit types to variables.

## Testing Guidelines

There is no standalone unit-test suite. Run formatting and validation for every
Terraform change; use the full pre-commit suite for module interfaces,
generated documentation, or wrapper changes. Generator
changes must reproduce committed preset modules without unexpected drift. Do
not run plans or applies against AWS merely for testing.

## Commit & Pull Request Guidelines

Recent history uses Conventional Commit prefixes such as `feat:`, `fix:`,
`docs:`, `ci:`, and `chore:`. Keep commits focused. PR titles must use one of
those types and begin the subject with a capital letter,
for example `feat: Add Redis preset`. PR descriptions should identify affected
modules, list validation performed, note generated files, link relevant issues,
and call out replacement, networking, or security-group rule impacts.

## Generated Files & Safety

Do not hand-edit generated preset modules or wrappers; change the catalog or
templates and regenerate. Do not edit `README.md` content between Terraform
Docs markers; change HCL and refresh it with pre-commit. Never commit
`.terraform/`, state files, saved plans, credentials, or provider debug logs.
Root or example `terraform apply` commands require explicit authorization
because they can change AWS infrastructure.
