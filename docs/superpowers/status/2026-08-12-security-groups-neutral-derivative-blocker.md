# Security Groups Neutral Derivative — Execution Blocker

- Branch: `neutral/v6.0.0-neutral.1`
- Approved upstream: `terraform-aws-modules/terraform-aws-security-group`, tag
  `v6.0.0`, commit `58d8e895915f5573767081142d063b7caf7a2b47`.

Execution is blocked by a conflict between the plan's enumerated
import/generated-document edit set, which requires strict parity, and the
repository-wide neutrality requirement. The extra upstream match is
`CHANGELOG.md:194`, outside that edit set.

No source change, generated-document change, pull request, tag, or release was
created. Safe resolution requires explicit authorization to neutralize the
changelog entry and update the parity and notice expectations accordingly.

Per user instruction, execution moved to RDS.
