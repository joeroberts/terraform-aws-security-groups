# Upstream provenance

This repository, `joeroberts/terraform-aws-security-groups`, is a clean-history derivative of [terraform-aws-modules/terraform-aws-security-group](https://github.com/terraform-aws-modules/terraform-aws-security-group).

## Imported release

- Upstream tag: `v6.0.0`
- Upstream commit: `58d8e895915f5573767081142d063b7caf7a2b47`
- Import date: `2026-08-12`
- Target repository: `joeroberts/terraform-aws-security-groups`
- Reserved neutral release tag: `v6.0.0-neutral.1`

The reserved tag identifies the intended derivative release. It is not created by the import process.

## Intentional differences

The imported root module removes one nontechnical input, makes the root creation gate depend only on `var.create`, and removes the corresponding root-wrapper forwarding argument. Related root documentation is removed, including the banner, generated input row, final informational section, and the authorized changelog bullet. Modification notices are added to the five changed upstream-derived files.

No technical input default or unrelated module behavior is changed. Because the removed gate input defaulted to `true`, the default effective creation behavior remains unchanged.

The 53-entry catalog in `generate/catalog.tf` remains the source of truth for generated preset modules and wrappers. Its upstream-derived content and generated HCL are preserved in this import; derivative maintainers own subsequent synchronization and regeneration.

## Authorship, maintenance, and license

Original module authorship and contributor credit belong to the upstream project. Maintenance of this derivative belongs to `joeroberts/terraform-aws-security-groups`; that maintenance does not imply authorship of unchanged upstream work.

The upstream Apache License 2.0 `LICENSE` file and applicable copyright and attribution notices are preserved. Modified upstream-derived files carry a dated notice that points back to this provenance record.

## Update process

Future updates must verify an exact upstream tag and commit in temporary storage, materialize its committed tree without Git metadata, apply and verify required sanitization there, and only then copy the sanitized tree into this repository. Updates must record the new provenance and intentional deltas. Upstream Git history must never be merged, rebased, or otherwise imported into this clean-history derivative.
