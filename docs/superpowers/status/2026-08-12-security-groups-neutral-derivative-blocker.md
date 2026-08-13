# Security Groups Neutral Derivative — Blocker Resolved

Status: the authorization blocker is resolved. Tasks 1-4 are complete and approved.
The final whole-branch pre-PR review ran against
`ae247df035f510a7b9398fd1694c2204f7daed4f`; its consolidated findings are
addressed by the commit containing this status update. A normal push, the
complete current Task 4 fence, and a scoped fix re-review are required before
PR creation.

Completed milestone commits:

- Task 1 sanitized import: `688ae2d8a0feb4a8793ed700373defa14af2b0be`.
- Task 2 generated/consumer documentation: `0b401f0451c601e7aa1a022476996fa74a7ffaa3`.
- Task 3 workflow hardening: `ae247df035f510a7b9398fd1694c2204f7daed4f`.
- Task 4 complete verification and task approval: unchanged source head
  `ae247df035f510a7b9398fd1694c2204f7daed4f`.

The whole-branch findings require one exact `.pre-commit-config.yaml`
derivative notice, the sole exact `--args=--dry-run` argument on the active
`terraform_wrapper_module_for_each` hook, a root-anchored `^variables\.tf$`
exclusion on `end-of-file-fixer` only, the PR title
`feat: Add neutral security group module v6.0.0`, and corrected status/source
evidence. Root `variables.tf` remains byte-identical to its authorized
transform. The derivative's 325 exact documentation source occurrences
comprise 217 active and 108 commented declarations.

The first actual all-files pre-commit attempt exposed missing local hook tools;
after installing the exact workflow versions, a second attempt proved the
inherited wrapper hook unconditionally overwrites 54 wrapper READMEs and
`wrappers/main.tf`. Reproduction under both the worktree basename and a CI-like
repository basename changed the same 55 paths, removed all 55 derivative
notices, and rewrote all 270 wrapper sources; basename affected only the wrong
Registry identity. The controller therefore authorized the hook's documented
dry-run mode. It remains active and exercises hcledit parsing and wrapper
generation feasibility for the root plus 53 modules without writing tracked
artifacts. Exact Task 4 parity remains authoritative for all 216 wrapper HCL
files, 270 wrapper sources, and 54 wrapper README notices.

- Branch: `neutral/v6.0.0-neutral.1`
- Approved upstream: `terraform-aws-modules/terraform-aws-security-group`, tag
  `v6.0.0`, commit `58d8e895915f5573767081142d063b7caf7a2b47`.

The prior conflict between strict parity and repository-wide neutrality is
resolved. The user authorized deletion of the complete forbidden bullet at
pristine `CHANGELOG.md:194`, with an exact first-line HTML modification notice.
Task 1 permits exactly five upstream-derived deltas: `README.md`,
`CHANGELOG.md`, `main.tf`, `variables.tf`, and `wrappers/main.tf`. The final
authorized notice-bearing delta is exactly 120 paths after generator-owned
documentation, wrapper documentation, six workflows, and the reviewed
`.pre-commit-config.yaml` exception are included.

The amended plan requires byte-exact deterministic transforms for both Markdown
files and all three changed HCL files; byte identity for every other HCL file;
an exact five-path Task 1 delta; 441-file HCL parity including exactly 216
wrapper HCL files; exact 325-source, 270-wrapper-source, 120-notice, and
54-wrapper-notice counts; structural/adversarial pre-commit gates; an active
54-input non-mutating wrapper dry run; actual all-files pre-commit execution;
fresh archived upstream references; and
fail-closed worklist, `rg`, `git grep`, history, synchronization, and no-tag
gates.

No pull request, tag, or release exists for Security Groups. PR creation, tag
creation, release work, and the IAM campaign journal update remain deferred
until the consolidated fix is pushed, fully reverified, and independently
re-reviewed.
