# Security Groups Neutral Derivative — Blocker Resolved

Status: authorization blocker resolved on 2026-08-12; Task 1 is committed and
pushed; Task 2 documentation changes are preserved uncommitted and paused;
review fix round 4 is pending independent approval.

The original authorization blocker and the first three review fixes are
resolved. Task 2 execution found that every pristine/current wrapper README
legitimately contains five `source =` occurrences: two active Terragrunt
sources, two commented Git alternatives, and one active Terraform source. The
plan correctly requires replacing all five without changing prose or HCL
inputs, but its gate incorrectly expected four. Round 4 corrects that count to
five and validates every active/commented occurrence against the exact
path-derived target and reserved ref. This review gap is not claimed closed
until the fix is committed, normally pushed, synchronized, and independently
reviewed. Task 2 execution remains paused.

- Branch: `neutral/v6.0.0-neutral.1`
- Approved upstream: `terraform-aws-modules/terraform-aws-security-group`, tag
  `v6.0.0`, commit `58d8e895915f5573767081142d063b7caf7a2b47`.

The prior conflict between strict parity and repository-wide neutrality is
resolved. The user authorized deletion of the complete forbidden bullet at
pristine `CHANGELOG.md:194`, with an exact first-line HTML modification notice.
Task 1 now permits exactly five upstream-derived deltas: `README.md`,
`CHANGELOG.md`, `main.tf`, `variables.tf`, and `wrappers/main.tf`.

The amended plan requires byte-exact deterministic transforms for both Markdown
files and all three changed HCL files; byte identity for every other HCL file;
an exact five-path delta; staged checks that include untracked imports; narrow
root README and root variables `blank-at-eof` exceptions bounded by earlier
exact expected/source/target `cmp` checks; fresh archived upstream references;
root-anchored exclusions; and fail-closed
worklist, `rg`, `git grep`, history, synchronization, and no-tag gates.

This round-4 resolution changes plan/status documentation only and does not
alter the preserved uncommitted Task 2 root/template/generated-module README
changes. No Task 2 commit, pull request, tag, or release exists.
