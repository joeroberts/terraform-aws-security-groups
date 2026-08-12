# Security Groups Neutral Derivative — Blocker Resolved

Status: authorization blocker resolved on 2026-08-12; Task 1 source import is
staged but paused before its commit; review fix round 3 is pending independent
approval.

The original authorization blocker is resolved. Review fix round 2 of 5 is
being incorporated for immediate post-first-apply HCL path parity and
structural workflow permissions/release-guard verification. These review gaps
were sufficient to begin Task 1. Execution then found that the exact authorized
`variables.tf` transform preserves a terminal blank which the original cached
whitespace gate rejected. Round 3 narrows only that `blank-at-eof` diagnostic
behind same-fence exact byte comparisons. This new review gap is not claimed
closed until the fix is committed, normally pushed, synchronized, and
independently reviewed. Source execution remains paused.

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

This round-3 resolution changes documentation only and does not alter the staged
Task 1 source import. No source commit, pull request, tag, or release exists.
