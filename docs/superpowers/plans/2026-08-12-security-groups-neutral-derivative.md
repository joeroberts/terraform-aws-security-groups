# Neutral AWS Security Groups Module Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a clean-history derivative of upstream AWS Security Group module v6.0.0, remove the nontechnical input and root creation gate, keep all 53 generated presets synchronized, and open a verified PR for reserved tag v6.0.0-neutral.1.

**Architecture:** Sanitize the exact singular-named upstream repository in temporary storage, then import it into the plural-named target. Change only the root `local.create` expression, deleted input, wrapper forwarding, documentation, generator-owned consumer sources, and inherited workflows; verify all 110 Terraform roots and re-run the catalog generator before PR creation.

**Tech Stack:** Terraform 1.15.7, Terraform >= 1.5.7, AWS provider >= 6.29, local provider >= 2.5, HCL templates, terraform-docs 0.24.0, TFLint 0.62.0, actionlint 1.7.7, Git, GitHub CLI

## Global Constraints

- Worktree: `/Users/jroberts/Documents/dev/joeroberts/terraform/.worktrees/terraform-aws-security-groups/v6.0.0-neutral.1`.
- Branch: `neutral/v6.0.0-neutral.1`; PR base: `main`.
- Baseline: `terraform-aws-modules/terraform-aws-security-group` tag `v6.0.0`, commit `58d8e895915f5573767081142d063b7caf7a2b47`.
- Target: `joeroberts/terraform-aws-security-groups` (plural); reserved tag `v6.0.0-neutral.1`.
- Do not create or push the reserved tag before review and merge.
- Preserve Apache 2.0, upstream attribution, provider metadata, 53-entry catalog, generated HCL, and unrelated behavior.
- Every changed upstream-derived file begins with `Modified by joeroberts/terraform-aws-security-groups on 2026-08-12; see UPSTREAM.md.` in its comment syntax.
- Root resource creation must depend only on `var.create`; remove the nontechnical variable and wrapper forwarding.
- The authorized Task 1 upstream-derived delta set is exactly `README.md`,
  `CHANGELOG.md`, `main.tf`, `variables.tf`, and `wrappers/main.tf`. Delete the
  complete forbidden bullet at pristine `CHANGELOG.md:194`, and make no other
  changelog change except its exact first-line notice.
- Upstream v6.0.0 contains no `*.tftest.hcl`; creation assertions, generator parity, full HCL parity, and 110-root init/validate are the focused behavioral tests.
- Never import upstream Git history or copy an unsanitized snapshot into the target.
- Before Task 1 imports source, commit and normally push this authorization
  amendment without force; require a clean worktree and exact local/remote
  branch synchronization.
- Every mutation or release-gating Bash fence is self-contained, begins with
  `set -euo pipefail`, defines every retained clone/import path it consumes,
  and uses no process substitution or `$(cat file)` consumer. Negative
  `rg`/`git grep` gates accept status 1 only and fail on status greater than 1.
- Create each pristine reference with `git archive` from one exact verified
  upstream clone. Never copy or otherwise import upstream Git metadata. Task 4
  creates and verifies its own fresh clone rather than relying on Task 1 state.
- Root exclusions are root-anchored: exclude `.git` whether it is a file or
  directory, the exact root `.superpowers/` scratch tree, and the two exact
  root planning/status documents without hiding nested namesakes.
- Keep `.superpowers/` ignored and untracked.
- Push each milestone without force. If blocked, persist and push `docs/neutralization/BLOCKER.md`, open a draft PR when coherent, update the IAM campaign journal, and proceed to RDS.

## File Map

- Import: complete upstream v6.0.0 working tree except `.git/`.
- Modify before copy: `main.tf:2`, `variables.tf:140-144`,
  `wrappers/main.tf:12`, `README.md:5,130,158-162`, and
  `CHANGELOG.md:194` (the complete authorized forbidden bullet).
- Modify after copy: `generate/templates/README.md.tftpl` — derivative Git source, no Registry version argument, local license, modification notice.
- Regenerate after template change: all 53 `modules/*/README.md`.
- Modify mechanically after copy: root `README.md` and all 54 `wrappers/**/README.md` files — derivative identity, sources, notices.
- Modify: six `.github/workflows/*` files — notices, action pins, permissions.
- Create: `UPSTREAM.md`.
- Preserve unchanged: `generate/catalog.tf`, other templates, all 53 preset-module HCL outputs, and unrelated external VPC example source.

---

### Task 1: Sanitized Root Import

**Required resumption gate:** Commit this plan/status amendment as
`docs: harden security groups plan review gates` and normally push it
to `neutral/v6.0.0-neutral.1` without force before running any Task 1 source
command. Task 1 itself proves that exact documentation-only commit is the clean,
synchronized branch tip.

**Files:**
- Modify: `main.tf`, `variables.tf`, `wrappers/main.tf`, `README.md`, `CHANGELOG.md`
- Create: `UPSTREAM.md`
- Import: remaining upstream tree

**Interfaces:**
- Consumes: upstream v6.0.0 and target license-only main
- Produces: neutral root module controlled solely by `var.create`

- [ ] **Step 1: Verify and publish the amendment, then archive one exact upstream clone**

```bash
set -euo pipefail
sg_branch='neutral/v6.0.0-neutral.1'
sg_expected_sha='58d8e895915f5573767081142d063b7caf7a2b47'
sg_amendment_parent='c86208cdcf143e84612d9a4000b053eeb08eb26a'
sg_retained_root="/private/tmp/terraform-aws-security-group-v6.0.0-$sg_expected_sha/task1-$(git rev-parse HEAD)"
sg_verified_clone="$sg_retained_root/verified-upstream"
sg_import_root="$sg_retained_root/import"
test "$(git branch --show-current)" = "neutral/v6.0.0-neutral.1"
test "$(git remote get-url origin)" = "git@github.com:joeroberts/terraform-aws-security-groups.git"
test -z "$(git status --porcelain)"
test "$(git log -1 --format=%s)" = \
  "docs: harden security groups plan review gates"
test "$(git rev-parse HEAD^)" = "$sg_amendment_parent"
sg_publication_gate=$(mktemp -d /private/tmp/terraform-aws-security-groups-publication.XXXXXX)
printf '%s\n' \
  'M docs/superpowers/plans/2026-08-12-security-groups-neutral-derivative.md' \
  'M docs/superpowers/status/2026-08-12-security-groups-neutral-derivative-blocker.md' \
  > "$sg_publication_gate/expected-scope"
git diff-tree --no-commit-id --name-status -r HEAD | sed $'s/\t/ /' | sort \
  > "$sg_publication_gate/actual-scope"
diff -u "$sg_publication_gate/expected-scope" \
  "$sg_publication_gate/actual-scope"
test -z "$(git ls-files .superpowers)"
git check-ignore -q \
  .superpowers/sdd/2026-08-12-security-groups-neutral-derivative/plan-amendment-report.md
git push origin "HEAD:refs/heads/$sg_branch"
git fetch origin "$sg_branch"
test "$(git rev-parse HEAD)" = "$(git rev-parse "origin/$sg_branch")"
test ! -e "$sg_retained_root"
mkdir -p "$sg_retained_root"
git clone --quiet --depth 1 --branch v6.0.0 \
  https://github.com/terraform-aws-modules/terraform-aws-security-group.git \
  "$sg_verified_clone"
test "$(git -C "$sg_verified_clone" rev-parse HEAD)" = "$sg_expected_sha"
test "$(git -C "$sg_verified_clone" rev-parse refs/tags/v6.0.0^{commit})" = \
  "$sg_expected_sha"
test -z "$(git -C "$sg_verified_clone" status --porcelain)"
mkdir -p "$sg_import_root/pristine" "$sg_import_root/source" \
  "$sg_import_root/expected"
git -C "$sg_verified_clone" archive --format=tar HEAD | \
  tar -xf - -C "$sg_import_root/pristine"
rsync -a "$sg_import_root/pristine/" "$sg_import_root/source/"
test ! -e "$sg_import_root/pristine/.git"
test ! -e "$sg_import_root/source/.git"
```

Expected: the exact two-document authorization amendment is the clean branch
tip and is synchronized by a normal push before any source clone occurs. The
scratch workspace remains ignored/untracked. Exactly one Task 1 upstream clone
is verified at the recorded tag/SHA; its committed tree is materialized with
`git archive`, so no upstream history enters either comparison tree.

- [ ] **Step 2: Prove pristine acceptance fails**

```bash
set -euo pipefail
sg_expected_sha='58d8e895915f5573767081142d063b7caf7a2b47'
sg_retained_root="/private/tmp/terraform-aws-security-group-v6.0.0-$sg_expected_sha/task1-$(git rev-parse HEAD)"
sg_verified_clone="$sg_retained_root/verified-upstream"
sg_import_root="$sg_retained_root/import"
sg_neutral_pattern="$(printf '%s|%s|%s|%s|%s|%s|%s' \
  'put''in' 'khuy''lo' 'ukr''ain' 'russ''ia' 'bela''rus' 'cri''mea' 'don''bas')"
sg_pristine_matches="$sg_import_root/expected/pristine-neutral-matches"
rg -l -i "$sg_neutral_pattern" "$sg_import_root/source" --hidden \
  > "$sg_pristine_matches"
test -s "$sg_pristine_matches"
if rg -n '^  create = var\.create$' "$sg_import_root/source/main.tf"; then
  exit 1
else
  sg_rg_status=$?
  test "$sg_rg_status" = "1"
fi
sed -n '194p' "$sg_import_root/pristine/CHANGELOG.md" \
  > "$sg_import_root/expected/changelog-authorized-bullet"
rg -q -i "$sg_neutral_pattern" \
  "$sg_import_root/expected/changelog-authorized-bullet"
```

Expected: forbidden matches exist, pristine `CHANGELOG.md:194` is one of them,
and the desired direct expression does not. Any `rg` operational error fails.

- [ ] **Step 3: Apply minimal neutralization before copy**

Resolve and verify the mutation target without consuming prior shell state:

```bash
set -euo pipefail
sg_branch='neutral/v6.0.0-neutral.1'
sg_expected_sha='58d8e895915f5573767081142d063b7caf7a2b47'
sg_amendment_parent='c86208cdcf143e84612d9a4000b053eeb08eb26a'
sg_expected_subject='docs: harden security groups plan review gates'
sg_current_head=$(git rev-parse HEAD)
sg_retained_root="/private/tmp/terraform-aws-security-group-v6.0.0-$sg_expected_sha/task1-$sg_current_head"
sg_verified_clone="$sg_retained_root/verified-upstream"
sg_import_root="$sg_retained_root/import"
test "$(git branch --show-current)" = "$sg_branch"
test "$(git remote get-url origin)" = \
  'git@github.com:joeroberts/terraform-aws-security-groups.git'
test -z "$(git status --porcelain)"
test "$(git log -1 --format=%s)" = "$sg_expected_subject"
test "$(git rev-parse HEAD^)" = "$sg_amendment_parent"
test "$sg_current_head" = "$(git rev-parse "origin/$sg_branch")"
test "$(git -C "$sg_verified_clone" rev-parse HEAD)" = "$sg_expected_sha"
test "$(git -C "$sg_verified_clone" rev-parse refs/tags/v6.0.0^{commit})" = \
  "$sg_expected_sha"
test -d "$sg_import_root/pristine"
test -d "$sg_import_root/source"
test -d "$sg_import_root/expected"
test ! -e "$sg_import_root/source/.git"
for sg_mutation_file in main.tf variables.tf wrappers/main.tf README.md CHANGELOG.md; do
  test -f "$sg_import_root/pristine/$sg_mutation_file"
  test -f "$sg_import_root/source/$sg_mutation_file"
  cmp "$sg_import_root/pristine/$sg_mutation_file" \
    "$sg_import_root/source/$sg_mutation_file"
  printf 'APPLY_PATCH_TARGET=%s\n' \
    "$sg_import_root/source/$sg_mutation_file"
done
```

Expected: the fence prints five literal absolute `APPLY_PATCH_TARGET` paths
under the verified current amendment HEAD. Copy those printed paths verbatim
into the five `apply_patch` file headers; `apply_patch` cannot expand shell
variables. Do not type `$sg_import_root` into a patch and do not infer or reuse
a path from another fence.

Using only those five printed absolute targets, and without running a generator:

- Change `main.tf:2` to `create = var.create`.
- Delete the complete variable block at `variables.tf:140-144`.
- Delete `wrappers/main.tf:12`.
- Delete exactly pristine `README.md:5`, the deleted-input row at
  `README.md:130`, and final section `README.md:158-162`.
- Delete the complete user-authorized bullet at pristine `CHANGELOG.md:194`.

Prepend the dated HCL notice to the three changed `.tf` files and the dated HTML
notice to both Markdown files. Their exact first lines are:

```text
# Modified by joeroberts/terraform-aws-security-groups on 2026-08-12; see UPSTREAM.md.
<!-- Modified by joeroberts/terraform-aws-security-groups on 2026-08-12; see UPSTREAM.md. -->
```

Then run:

```bash
set -euo pipefail
sg_expected_sha='58d8e895915f5573767081142d063b7caf7a2b47'
sg_retained_root="/private/tmp/terraform-aws-security-group-v6.0.0-$sg_expected_sha/task1-$(git rev-parse HEAD)"
sg_verified_clone="$sg_retained_root/verified-upstream"
sg_import_root="$sg_retained_root/import"
sg_neutral_pattern="$(printf '%s|%s|%s|%s|%s|%s|%s' \
  'put''in' 'khuy''lo' 'ukr''ain' 'russ''ia' 'bela''rus' 'cri''mea' 'don''bas')"
sg_create_matches="$sg_import_root/expected/direct-create-matches"
rg -n '^  create = var\.create$' "$sg_import_root/source/main.tf" \
  > "$sg_create_matches"
test "$(wc -l < "$sg_create_matches" | tr -d '[:space:]')" = "1"
if rg -n -i "$sg_neutral_pattern" "$sg_import_root/source" \
  --hidden; then
  exit 1
else
  sg_rg_status=$?
  test "$sg_rg_status" = "1"
fi
```

- [ ] **Step 4: Copy the sanitized tree and create provenance**

```bash
set -euo pipefail
sg_expected_sha='58d8e895915f5573767081142d063b7caf7a2b47'
sg_retained_root="/private/tmp/terraform-aws-security-group-v6.0.0-$sg_expected_sha/task1-$(git rev-parse HEAD)"
sg_verified_clone="$sg_retained_root/verified-upstream"
sg_import_root="$sg_retained_root/import"
rsync -a --exclude='/.git' "$sg_import_root/source/" ./
test -f generate/catalog.tf
test -f CHANGELOG.md
test "$(find modules -mindepth 1 -maxdepth 1 -type d | wc -l | tr -d '[:space:]')" = "53"
test "$(find wrappers -mindepth 1 -maxdepth 1 -type d | wc -l | tr -d '[:space:]')" = "53"
test -f docs/superpowers/plans/2026-08-12-security-groups-neutral-derivative.md
test -f docs/superpowers/status/2026-08-12-security-groups-neutral-derivative-blocker.md
```

Create `UPSTREAM.md` with the singular upstream URL, exact tag/SHA/import date,
plural target name, reserved neutral tag, intentional input/gate/wrapper/docs
deltas including the authorized changelog-bullet deletion, unchanged-default
statement, Apache notices, upstream authorship versus derivative maintenance,
catalog ownership, and an update process that sanitizes before copy and never
merges upstream history.

- [ ] **Step 5: Prove the exact five-path imported delta**

```bash
set -euo pipefail
sg_expected_sha='58d8e895915f5573767081142d063b7caf7a2b47'
sg_retained_root="/private/tmp/terraform-aws-security-group-v6.0.0-$sg_expected_sha/task1-$(git rev-parse HEAD)"
sg_verified_clone="$sg_retained_root/verified-upstream"
sg_import_root="$sg_retained_root/import"
sg_notice='Modified by joeroberts/terraform-aws-security-groups on 2026-08-12; see UPSTREAM.md.'
sg_hcl_notice="# $sg_notice"
sg_markdown_notice="<!-- $sg_notice -->"
for sg_hcl_file in main.tf variables.tf wrappers/main.tf; do
  test "$(head -n 1 "$sg_import_root/source/$sg_hcl_file")" = "$sg_hcl_notice"
  test "$(head -n 1 "$sg_hcl_file")" = "$sg_hcl_notice"
done
for sg_markdown_file in README.md CHANGELOG.md; do
  test "$(head -n 1 "$sg_import_root/source/$sg_markdown_file")" = \
    "$sg_markdown_notice"
  test "$(head -n 1 "$sg_markdown_file")" = "$sg_markdown_notice"
done
perl -pe 's/create = var\.create && var\.[A-Za-z0-9_]+/create = var.create/' \
  "$sg_import_root/pristine/main.tf" > "$sg_import_root/expected/main.tf"
sed '1d' main.tf > "$sg_import_root/expected/actual-main.tf"
cmp "$sg_import_root/expected/main.tf" \
  "$sg_import_root/expected/actual-main.tf"
sed '140,144d' "$sg_import_root/pristine/variables.tf" \
  > "$sg_import_root/expected/variables.tf"
sed '1d' variables.tf > "$sg_import_root/expected/actual-variables.tf"
cmp "$sg_import_root/expected/variables.tf" \
  "$sg_import_root/expected/actual-variables.tf"
sed '12d' "$sg_import_root/pristine/wrappers/main.tf" \
  > "$sg_import_root/expected/wrappers-main.tf"
sed '1d' wrappers/main.tf > "$sg_import_root/expected/actual-wrappers-main.tf"
cmp "$sg_import_root/expected/wrappers-main.tf" \
  "$sg_import_root/expected/actual-wrappers-main.tf"
{
  printf '%s\n' "$sg_markdown_notice"
  sed '5d;130d;158,162d' "$sg_import_root/pristine/README.md"
} > "$sg_import_root/expected/README.md"
{
  printf '%s\n' "$sg_markdown_notice"
  sed '194d' "$sg_import_root/pristine/CHANGELOG.md"
} > "$sg_import_root/expected/CHANGELOG.md"
cmp "$sg_import_root/expected/README.md" \
  "$sg_import_root/source/README.md"
cmp "$sg_import_root/expected/README.md" README.md
cmp "$sg_import_root/expected/CHANGELOG.md" \
  "$sg_import_root/source/CHANGELOG.md"
cmp "$sg_import_root/expected/CHANGELOG.md" CHANGELOG.md
sg_pristine_worklist="$sg_import_root/expected/pristine-worklist"
git -C "$sg_verified_clone" ls-tree -r --name-only HEAD | sort \
  > "$sg_pristine_worklist"
sg_pristine_count=$(wc -l < "$sg_pristine_worklist" | tr -d '[:space:]')
case "$sg_pristine_count" in
  ''|*[!0-9]*) exit 1 ;;
esac
test "$sg_pristine_count" -gt "0"
sg_changed_paths=()
sg_compared_count=0
while IFS= read -r sg_path; do
  test -n "$sg_path"
  sg_compared_count=$((sg_compared_count + 1))
  if ! cmp -s "$sg_import_root/pristine/$sg_path" "$sg_path"; then
    sg_changed_paths[${#sg_changed_paths[@]}]="$sg_path"
  fi
done < "$sg_pristine_worklist"
test "$sg_compared_count" -eq "$sg_pristine_count"
test "${#sg_changed_paths[@]}" -eq "5"
printf '%s\n' CHANGELOG.md README.md main.tf variables.tf wrappers/main.tf \
  > "$sg_import_root/expected/authorized-paths"
printf '%s\n' "${sg_changed_paths[@]}" | sort \
  > "$sg_import_root/expected/actual-changed-paths"
diff -u "$sg_import_root/expected/authorized-paths" \
  "$sg_import_root/expected/actual-changed-paths"
sg_imported_file_set="$sg_import_root/expected/imported-file-set"
find . \( -path './.git' -o -path './.superpowers' \) -prune -o \
  -type f -print0 > "$sg_import_root/expected/target-files-nul"
while IFS= read -r -d '' sg_target_file; do
  sg_target_path=${sg_target_file#./}
  case "$sg_target_path" in
    UPSTREAM.md|docs/superpowers/plans/2026-08-12-security-groups-neutral-derivative.md|docs/superpowers/status/2026-08-12-security-groups-neutral-derivative-blocker.md)
      continue
      ;;
  esac
  printf '%s\n' "$sg_target_path"
done < "$sg_import_root/expected/target-files-nul" | sort \
  > "$sg_imported_file_set"
diff -u "$sg_pristine_worklist" "$sg_imported_file_set"
```

Expected: `README.md` and `CHANGELOG.md` are byte-identical to their
deterministic notice-plus-deletion transforms. The README comparison preserves
the terminal blank left by deleting pristine lines 158-162. Exactly five
pristine paths differ, the three changed HCL files match their exact transforms,
every other HCL remains byte-identical as part of the full pristine worklist,
and root-only exclusions do not hide nested `.git`, `.superpowers`, or planning
namesakes.

- [ ] **Step 6: Stage, whitespace-check, commit, and push the import**

```bash
set -euo pipefail
sg_expected_sha='58d8e895915f5573767081142d063b7caf7a2b47'
sg_retained_root="/private/tmp/terraform-aws-security-group-v6.0.0-$sg_expected_sha/task1-$(git rev-parse HEAD)"
sg_verified_clone="$sg_retained_root/verified-upstream"
sg_import_root="$sg_retained_root/import"
sg_markdown_notice='<!-- Modified by joeroberts/terraform-aws-security-groups on 2026-08-12; see UPSTREAM.md. -->'
{
  printf '%s\n' "$sg_markdown_notice"
  sed '5d;130d;158,162d' "$sg_import_root/pristine/README.md"
} > "$sg_import_root/expected/README-precommit.md"
cmp "$sg_import_root/expected/README-precommit.md" \
  "$sg_import_root/source/README.md"
cmp "$sg_import_root/expected/README-precommit.md" README.md
terraform fmt -check -recursive
git add --all -- . \
  ':(top,exclude)docs/superpowers/plans/2026-08-12-security-groups-neutral-derivative.md' \
  ':(top,exclude)docs/superpowers/status/2026-08-12-security-groups-neutral-derivative-blocker.md' \
  ':(top,exclude).superpowers/**'
sg_staged_paths="$sg_import_root/expected/staged-paths-nul"
git diff --cached --name-only -z > "$sg_staged_paths"
test -s "$sg_staged_paths"
while IFS= read -r -d '' sg_staged_path; do
  case "$sg_staged_path" in
    docs/superpowers/plans/2026-08-12-security-groups-neutral-derivative.md|docs/superpowers/status/2026-08-12-security-groups-neutral-derivative-blocker.md|.superpowers|.superpowers/*)
      exit 1
      ;;
  esac
done < "$sg_staged_paths"
git diff --cached --name-only -- UPSTREAM.md \
  > "$sg_import_root/expected/staged-provenance"
test -s "$sg_import_root/expected/staged-provenance"
git diff --cached --name-only -- README.md \
  > "$sg_import_root/expected/staged-root-readme"
test -s "$sg_import_root/expected/staged-root-readme"
git diff --cached --check -- . ':(top,exclude)README.md'
git -c core.whitespace=-blank-at-eof diff --cached --check -- README.md
git commit -m "feat: import neutral security group module v6.0.0"
git push -u origin neutral/v6.0.0-neutral.1
git fetch origin neutral/v6.0.0-neutral.1
test "$(git rev-parse HEAD)" = \
  "$(git rev-parse origin/neutral/v6.0.0-neutral.1)"
```

Expected: intended root import/provenance paths are staged before whitespace
gating, so untracked imported files cannot escape the check. Default cached
whitespace checks apply to every non-root-README path. The README-only cached
check disables only `blank-at-eof`, and is safe because the exact expected and
target README bytes were compared earlier in this same fail-closed fence. No
dynamic `git diff --no-index --check` producer controls the exception. The
neutral import is committed and normally pushed as one synchronized milestone.

---

### Task 2: Generator-Owned and Wrapper Consumer Documentation

**Files:**
- Modify: `README.md`
- Modify: `generate/templates/README.md.tftpl`
- Regenerate: 53 `modules/*/README.md`
- Modify: `wrappers/README.md` and 53 `wrappers/*/README.md`

**Interfaces:**
- Consumes: 53-entry `local.catalog` and reserved tag
- Produces: 108 derivative-facing README files with correct Git sources and stable generated module docs

- [ ] **Step 1: Prove consumer-source acceptance fails**

```bash
set -euo pipefail
sg_acceptance_root=$(mktemp -d /private/tmp/terraform-aws-security-groups-acceptance.XXXXXX)
rg -l 'terraform-aws-modules/security-group/aws|tfr:///terraform-aws-modules/security-group/aws' \
  README.md modules wrappers -g README.md \
  > "$sg_acceptance_root/upstream-consumer-sources"
test "$(wc -l < "$sg_acceptance_root/upstream-consumer-sources" | tr -d '[:space:]')" = "108"
```

Expected: root, 53 preset modules, and 54 wrapper documents still contain active upstream sources.

- [ ] **Step 2: Change the generator template, not generated module files**

Using `apply_patch`, change `generate/templates/README.md.tftpl` so it begins with
the dated HTML notice, emits this source, omits the Registry-only `version`
argument, and uses `[LICENSE](../../LICENSE)`:

```hcl
source = "git::ssh://git@github.com/joeroberts/terraform-aws-security-groups.git//modules/${name}?ref=v6.0.0-neutral.1"
```

Initialize/apply the generator, then regenerate Terraform docs:

```bash
set -euo pipefail
sg_generator_root="/private/tmp/terraform-aws-security-groups-generator/task2-$(git rev-parse HEAD)"
sg_hcl_worklist="$sg_generator_root/module-hcl-files"
sg_hcl_pristine_manifest="$sg_generator_root/module-hcl-pristine"
sg_hcl_first_apply_manifest="$sg_generator_root/module-hcl-first-apply"
sg_module_readmes="$sg_generator_root/module-readmes"
sg_module_doc_dirs="$sg_generator_root/module-doc-directories"
test ! -e "$sg_generator_root"
mkdir -p "$sg_generator_root"
find modules -type f -name '*.tf' | sort > "$sg_hcl_worklist"
sg_hcl_worklist_count=$(wc -l < "$sg_hcl_worklist" | tr -d '[:space:]')
case "$sg_hcl_worklist_count" in
  ''|*[!0-9]*) exit 1 ;;
esac
test "$sg_hcl_worklist_count" = "212"
sg_hcl_manifest_count=0
while IFS= read -r sg_hcl_file; do
  test -n "$sg_hcl_file"
  sg_hcl_manifest_count=$((sg_hcl_manifest_count + 1))
  shasum -a 256 "$sg_hcl_file"
done < "$sg_hcl_worklist" > "$sg_hcl_pristine_manifest"
test "$sg_hcl_manifest_count" -eq "$sg_hcl_worklist_count"
test "$(wc -l < "$sg_hcl_pristine_manifest" | tr -d '[:space:]')" = "212"
terraform -chdir=generate init -backend=false -input=false
terraform -chdir=generate apply -auto-approve -input=false
sg_hcl_manifest_count=0
while IFS= read -r sg_hcl_file; do
  test -n "$sg_hcl_file"
  sg_hcl_manifest_count=$((sg_hcl_manifest_count + 1))
  shasum -a 256 "$sg_hcl_file"
done < "$sg_hcl_worklist" > "$sg_hcl_first_apply_manifest"
test "$sg_hcl_manifest_count" -eq "$sg_hcl_worklist_count"
test "$(wc -l < "$sg_hcl_first_apply_manifest" | tr -d '[:space:]')" = "212"
cmp "$sg_hcl_pristine_manifest" "$sg_hcl_first_apply_manifest"
find modules -mindepth 2 -maxdepth 2 -type f -name README.md | sort \
  > "$sg_module_readmes"
test "$(wc -l < "$sg_module_readmes" | tr -d '[:space:]')" = "53"
while IFS= read -r sg_module_readme; do
  dirname "$sg_module_readme"
done < "$sg_module_readmes" > "$sg_module_doc_dirs"
test "$(wc -l < "$sg_module_doc_dirs" | tr -d '[:space:]')" = "53"
while IFS= read -r sg_docs_dir; do
  go run github.com/terraform-docs/terraform-docs@v0.24.0 markdown table \
    --lockfile=false --output-file README.md --output-mode inject "$sg_docs_dir"
done < "$sg_module_doc_dirs"
```

Expected: the deterministic ignored temporary state captures all 212 generated
module HCL hashes before the first generator apply, the first apply is
immediately byte-stable against that pristine manifest, and exactly 53 module
READMEs are regenerated from the modified template.

- [ ] **Step 3: Update root identity and sources**

In `README.md`, add derivative identity/provenance and reserved-tag guidance,
keep upstream authors/contributors, add `joeroberts` as derivative maintainer,
use local `LICENSE`, and point the Complete example link to the target reserved
tag. Replace root and PostgreSQL preset sources with:

```hcl
source = "git::ssh://git@github.com/joeroberts/terraform-aws-security-groups.git?ref=v6.0.0-neutral.1"
source = "git::ssh://git@github.com/joeroberts/terraform-aws-security-groups.git//modules/postgresql?ref=v6.0.0-neutral.1"
```

The first-line notice already exists from Task 1.

- [ ] **Step 4: Mechanically update all 54 wrapper READMEs**

For each `wrappers/**/README.md`, derive its path relative to the repository and
mechanically replace Terraform, Terragrunt, and commented Git sources with:

```bash
set -euo pipefail
sg_wrapper_root=$(mktemp -d /private/tmp/terraform-aws-security-groups-wrappers.XXXXXX)
sg_wrapper_readmes="$sg_wrapper_root/readmes"
find wrappers -type f -name README.md | sort > "$sg_wrapper_readmes"
test "$(wc -l < "$sg_wrapper_readmes" | tr -d '[:space:]')" = "54"
while IFS= read -r sg_wrapper_readme; do
  sg_wrapper_path=${sg_wrapper_readme%/README.md}
  sg_wrapper_source="git::ssh://git@github.com/joeroberts/terraform-aws-security-groups.git//$sg_wrapper_path?ref=v6.0.0-neutral.1"
  printf '%s -> %s\n' "$sg_wrapper_readme" "$sg_wrapper_source"
done < "$sg_wrapper_readmes"
```

Prepend the dated HTML notice to every wrapper README. This is an approved bulk
mechanical rewrite; do not change wrapper prose or HCL inputs.

- [ ] **Step 5: Prove generated HCL and consumer docs are correct**

```bash
set -euo pipefail
sg_generation_gate=$(mktemp -d /private/tmp/terraform-aws-security-groups-generation.XXXXXX)
sg_generator_root="/private/tmp/terraform-aws-security-groups-generator/task2-$(git rev-parse HEAD)"
sg_hcl_worklist="$sg_generator_root/module-hcl-files"
sg_hcl_pristine_manifest="$sg_generator_root/module-hcl-pristine"
sg_hcl_pre_second_manifest="$sg_generation_gate/module-hcl-pre-second-apply"
sg_hcl_after_manifest="$sg_generation_gate/module-hcl-after-second-apply"
sg_module_readmes="$sg_generation_gate/module-readmes"
sg_module_doc_dirs="$sg_generation_gate/module-doc-directories"
test -d "$sg_generator_root"
test -s "$sg_hcl_worklist"
test -s "$sg_hcl_pristine_manifest"
test "$(wc -l < "$sg_hcl_worklist" | tr -d '[:space:]')" = "212"
test "$(wc -l < "$sg_hcl_pristine_manifest" | tr -d '[:space:]')" = "212"
find modules -type f -name '*.tf' | sort \
  > "$sg_generation_gate/current-module-hcl-files"
cmp "$sg_hcl_worklist" "$sg_generation_gate/current-module-hcl-files"
sg_hcl_manifest_count=0
while IFS= read -r sg_hcl_file; do
  test -n "$sg_hcl_file"
  sg_hcl_manifest_count=$((sg_hcl_manifest_count + 1))
  shasum -a 256 "$sg_hcl_file"
done < "$sg_hcl_worklist" > "$sg_hcl_pre_second_manifest"
test "$sg_hcl_manifest_count" = "212"
test "$(wc -l < "$sg_hcl_pre_second_manifest" | tr -d '[:space:]')" = "212"
cmp "$sg_hcl_pristine_manifest" "$sg_hcl_pre_second_manifest"
terraform -chdir=generate apply -auto-approve -input=false
sg_hcl_manifest_count=0
while IFS= read -r sg_hcl_file; do
  test -n "$sg_hcl_file"
  sg_hcl_manifest_count=$((sg_hcl_manifest_count + 1))
  shasum -a 256 "$sg_hcl_file"
done < "$sg_hcl_worklist" > "$sg_hcl_after_manifest"
test "$sg_hcl_manifest_count" = "212"
test "$(wc -l < "$sg_hcl_after_manifest" | tr -d '[:space:]')" = "212"
cmp "$sg_hcl_pristine_manifest" "$sg_hcl_after_manifest"
find modules -mindepth 2 -maxdepth 2 -type f -name README.md | sort \
  > "$sg_module_readmes"
test "$(wc -l < "$sg_module_readmes" | tr -d '[:space:]')" = "53"
while IFS= read -r sg_module_readme; do
  dirname "$sg_module_readme"
done < "$sg_module_readmes" > "$sg_module_doc_dirs"
test "$(wc -l < "$sg_module_doc_dirs" | tr -d '[:space:]')" = "53"
while IFS= read -r sg_docs_dir; do
  go run github.com/terraform-docs/terraform-docs@v0.24.0 markdown table \
    --lockfile=false --output-file README.md --output-mode inject "$sg_docs_dir"
done < "$sg_module_doc_dirs"
if rg -n 'source\s*=\s*"(terraform-aws-modules/security-group/aws|tfr:///terraform-aws-modules/security-group/aws)' \
  README.md modules wrappers -g README.md; then
  exit 1
else
  sg_rg_status=$?
  test "$sg_rg_status" = "1"
fi
sg_expected_documents="$sg_generation_gate/expected-documents"
sg_root_source='git::ssh://git@github.com/joeroberts/terraform-aws-security-groups.git?ref=v6.0.0-neutral.1'
sg_root_postgresql_source='git::ssh://git@github.com/joeroberts/terraform-aws-security-groups.git//modules/postgresql?ref=v6.0.0-neutral.1'
printf 'README.md\t%s\n' "$sg_root_source" > "$sg_expected_documents"
while IFS= read -r sg_module_readme; do
  sg_module_path=${sg_module_readme%/README.md}
  sg_module_name=${sg_module_path#modules/}
  printf '%s\tgit::ssh://git@github.com/joeroberts/terraform-aws-security-groups.git//modules/%s?ref=v6.0.0-neutral.1\n' \
    "$sg_module_readme" "$sg_module_name"
done < "$sg_module_readmes" >> "$sg_expected_documents"
sg_wrapper_readmes="$sg_generation_gate/wrapper-readmes"
find wrappers -type f -name README.md | sort > "$sg_wrapper_readmes"
test "$(wc -l < "$sg_wrapper_readmes" | tr -d '[:space:]')" = "54"
while IFS= read -r sg_wrapper_readme; do
  sg_wrapper_path=${sg_wrapper_readme%/README.md}
  printf '%s\tgit::ssh://git@github.com/joeroberts/terraform-aws-security-groups.git//%s?ref=v6.0.0-neutral.1\n' \
    "$sg_wrapper_readme" "$sg_wrapper_path"
done < "$sg_wrapper_readmes" >> "$sg_expected_documents"
sg_expected_document_count=$(wc -l < "$sg_expected_documents" | tr -d '[:space:]')
test "$sg_expected_document_count" = "108"
sg_checked_document_count=0
sg_root_source_seen=0
sg_root_postgresql_source_seen=0
while IFS=$'\t' read -r sg_document sg_expected_source; do
  test -n "$sg_document"
  test -n "$sg_expected_source"
  sg_checked_document_count=$((sg_checked_document_count + 1))
  sg_source_lines="$sg_generation_gate/source-lines-$sg_checked_document_count"
  rg -n 'source[[:space:]]*=' "$sg_document" > "$sg_source_lines"
  test -s "$sg_source_lines"
  sg_source_line_count=$(wc -l < "$sg_source_lines" | tr -d '[:space:]')
  case "$sg_document" in
    README.md) test "$sg_source_line_count" = "2" ;;
    modules/*/README.md) test "$sg_source_line_count" = "1" ;;
    wrappers/README.md|wrappers/*/README.md)
      test "$sg_source_line_count" = "4"
      ;;
    *) exit 1 ;;
  esac
  sg_checked_source_count=0
  while IFS= read -r sg_source_line; do
    sg_checked_source_count=$((sg_checked_source_count + 1))
    printf '%s\n' "$sg_source_line" | \
      sed -nE 's/.*source[[:space:]]*=[[:space:]]*"([^"]+)".*/\1/p' \
      > "$sg_generation_gate/source-value"
    test "$(wc -l < "$sg_generation_gate/source-value" | tr -d '[:space:]')" = "1"
    IFS= read -r sg_actual_source < "$sg_generation_gate/source-value"
    if test "$sg_document" = "README.md"; then
      case "$sg_actual_source" in
        "$sg_root_source") sg_root_source_seen=1 ;;
        "$sg_root_postgresql_source") sg_root_postgresql_source_seen=1 ;;
        *) exit 1 ;;
      esac
    else
      test "$sg_actual_source" = "$sg_expected_source"
    fi
  done < "$sg_source_lines"
  test "$sg_checked_source_count" -eq "$sg_source_line_count"
done < "$sg_expected_documents"
test "$sg_checked_document_count" -eq "$sg_expected_document_count"
test "$sg_root_source_seen" = "1"
test "$sg_root_postgresql_source_seen" = "1"
```

Expected: both later HCL manifests remain byte-identical to the retained
pre-first-apply 212-file manifest. All 108 expected documents are derived from
their paths and every active/commented `source =` value equals its exact target
repository, exact root/module/wrapper subdirectory, and exact reserved ref; the
root README contains both its root and PostgreSQL sources. A correct tag paired
with a wrong subpath fails.

- [ ] **Step 6: Commit and push documentation generation**

```bash
set -euo pipefail
git diff --check
git add README.md generate/templates/README.md.tftpl modules/*/README.md wrappers/README.md wrappers/*/README.md
git commit -m "docs: generate neutral security group sources"
git push
```

---

### Task 3: Six-Workflow Security Hardening

**Files:**
- Modify: `.github/workflows/generate-modules.yml`, `lock.yml`, `pr-title.yml`, `pre-commit.yml`, `release.yml`, `stale-actions.yaml`

**Interfaces:**
- Consumes: inherited tags/major branch
- Produces: six permission maps, full-SHA refs, actionlint-clean workflows, inert release automation

- [ ] **Step 1: Apply exact action pins**

First require the acceptance check to find unpinned refs:

```bash
set -euo pipefail
sg_workflow_gate=$(mktemp -d /private/tmp/terraform-aws-security-groups-workflows.XXXXXX)
rg -n -P 'uses:\s+[^\s#]+@(?![0-9a-f]{40}(?:\s|$))' .github/workflows \
  > "$sg_workflow_gate/unpinned"
test -s "$sg_workflow_gate/unpinned"
```

Then use `apply_patch` with this exact mapping and version comments:

| Ref | SHA |
| --- | --- |
| `actions/checkout@v5` | `fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5` |
| `actions/setup-node@v6` | `249970729cb0ef3589644e2896645e5dc5ba9c38 # v6` |
| `actions/stale@v10` | `1e223db275d687790206a7acac4d1a11bd6fe629 # v10` |
| `amannn/action-semantic-pull-request@v6.1.1` | `48f256284bd46cdaab1048c3721360e808335d50 # v6.1.1` |
| `clowdhaus/terraform-composite-actions/directories@v1.14.0` | `462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0` |
| `clowdhaus/terraform-composite-actions/pre-commit@v1.14.0` | `462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0` |
| `clowdhaus/terraform-min-max@v2.1.0` | `a86951cbe89f4d15caec805f36aa1dd68863ae32 # v2.1.0` |
| `cycjimmy/semantic-release-action@v5` | `ba330626c4750c19d8299de843f05c7aa5574f62 # v5 branch; tag v5.0.2` |
| `dessant/lock-threads@v5` | `1bf7ec25051fe7c00bdd17e6a7cf3d7bfb7dc771 # v5` |
| `hashicorp/setup-terraform@v3` | `b9cd54a3c349d3f38e8881555d616ced269862dd # v3` |
| `jaxxstorm/action-install-gh-release@v2.1.0` | `6096f2a2bbfee498ced520b6922ac2c06e990ed2 # v2.1.0` |

- [ ] **Step 2: Add notices and permissions**

Prepend the dated YAML notice to all six files. Add top-level permissions:

- `generate-modules.yml`: `contents: read`
- `lock.yml`: `issues: write`, `pull-requests: write`
- `pr-title.yml`: `pull-requests: read`
- `pre-commit.yml`: `contents: read`
- `release.yml`: `contents: read`
- `stale-actions.yaml`: `issues: write`, `pull-requests: write`

Retain the upstream-owner guard in `release.yml`, keeping derivative release automation inert.

- [ ] **Step 3: Validate, commit, and push workflows**

```bash
set -euo pipefail
if rg -n -P 'uses:\s+[^\s#]+@(?![0-9a-f]{40}(?:\s|$))' .github/workflows; then
  exit 1
else
  sg_rg_status=$?
  test "$sg_rg_status" = "1"
fi
sg_workflow_gate=$(mktemp -d /private/tmp/terraform-aws-security-groups-workflows.XXXXXX)
sg_workflow_files="$sg_workflow_gate/workflow-files"
find .github/workflows -maxdepth 1 -type f | sort > "$sg_workflow_files"
test "$(wc -l < "$sg_workflow_files" | tr -d '[:space:]')" = "6"
sed -nE 's/^[[:space:]]*-[[:space:]]*(uses:.*)$/\1/p; s/^[[:space:]]+(uses:.*)$/\1/p' \
  .github/workflows/* | sort > "$sg_workflow_gate/actual-actions"
printf '%s\n' \
  'uses: actions/checkout@fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5' \
  'uses: actions/checkout@fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5' \
  'uses: actions/checkout@fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5' \
  'uses: actions/checkout@fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5' \
  'uses: actions/checkout@fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5' \
  'uses: actions/setup-node@249970729cb0ef3589644e2896645e5dc5ba9c38 # v6' \
  'uses: actions/stale@1e223db275d687790206a7acac4d1a11bd6fe629 # v10' \
  'uses: amannn/action-semantic-pull-request@48f256284bd46cdaab1048c3721360e808335d50 # v6.1.1' \
  'uses: clowdhaus/terraform-composite-actions/directories@462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0' \
  'uses: clowdhaus/terraform-composite-actions/pre-commit@462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0' \
  'uses: clowdhaus/terraform-composite-actions/pre-commit@462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0' \
  'uses: clowdhaus/terraform-composite-actions/pre-commit@462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0' \
  'uses: clowdhaus/terraform-min-max@a86951cbe89f4d15caec805f36aa1dd68863ae32 # v2.1.0' \
  'uses: clowdhaus/terraform-min-max@a86951cbe89f4d15caec805f36aa1dd68863ae32 # v2.1.0' \
  'uses: cycjimmy/semantic-release-action@ba330626c4750c19d8299de843f05c7aa5574f62 # v5 branch; tag v5.0.2' \
  'uses: dessant/lock-threads@1bf7ec25051fe7c00bdd17e6a7cf3d7bfb7dc771 # v5' \
  'uses: hashicorp/setup-terraform@b9cd54a3c349d3f38e8881555d616ced269862dd # v3' \
  'uses: jaxxstorm/action-install-gh-release@6096f2a2bbfee498ced520b6922ac2c06e990ed2 # v2.1.0' \
  'uses: jaxxstorm/action-install-gh-release@6096f2a2bbfee498ced520b6922ac2c06e990ed2 # v2.1.0' \
  | sort > "$sg_workflow_gate/expected-actions"
test "$(wc -l < "$sg_workflow_gate/actual-actions" | tr -d '[:space:]')" = "19"
cmp "$sg_workflow_gate/expected-actions" "$sg_workflow_gate/actual-actions"
while IFS= read -r sg_workflow_file; do
  test "$(rg -c '^permissions:$' "$sg_workflow_file")" = "1"
  awk '
    $0 == "permissions:" { capture = 1; print; next }
    capture && /^  [a-z-]+: (read|write)$/ { print; next }
    capture { exit }
  ' "$sg_workflow_file" > "$sg_workflow_gate/actual-permissions"
  case "$(basename "$sg_workflow_file")" in
    generate-modules.yml|pre-commit.yml|release.yml)
      printf '%s\n' 'permissions:' '  contents: read' \
        > "$sg_workflow_gate/expected-permissions"
      ;;
    pr-title.yml)
      printf '%s\n' 'permissions:' '  pull-requests: read' \
        > "$sg_workflow_gate/expected-permissions"
      ;;
    lock.yml|stale-actions.yaml)
      printf '%s\n' 'permissions:' '  issues: write' '  pull-requests: write' \
        > "$sg_workflow_gate/expected-permissions"
      ;;
    *) exit 1 ;;
  esac
  cmp "$sg_workflow_gate/expected-permissions" \
    "$sg_workflow_gate/actual-permissions"
done < "$sg_workflow_files"
rg -Fxq "    if: github.repository_owner == 'terraform-aws-modules'" \
  .github/workflows/release.yml
go run github.com/rhysd/actionlint/cmd/actionlint@v1.7.7
git diff --check
git add .github/workflows
git commit -m "ci: pin and restrict inherited workflows"
git push
```

---

### Task 4: Generator, 110-Root, Parity, and Neutrality Verification

**Files:**
- Verify: all tracked files, generated artifacts, and complete target history
- Do not create: tracked `.terraform`, state, lock, cache, or test artifacts

**Interfaces:**
- Consumes: Tasks 1-3
- Produces: complete verification evidence and a clean synchronized branch

- [ ] **Step 1: Regenerate in the required order and prove stability**

```bash
set -euo pipefail
sg_verification_root=$(mktemp -d /private/tmp/terraform-aws-security-groups-verification.XXXXXX)
git log --all --format='%H%x09%s' > "$sg_verification_root/history-subjects"
rg $'\tfeat: import neutral security group module v6\.0\.0$' \
  "$sg_verification_root/history-subjects" \
  > "$sg_verification_root/import-commit"
test "$(wc -l < "$sg_verification_root/import-commit" | tr -d '[:space:]')" = "1"
IFS=$'\t' read -r sg_import_head sg_import_subject \
  < "$sg_verification_root/import-commit"
test "$sg_import_subject" = "feat: import neutral security group module v6.0.0"
test "$(git cat-file -t "$sg_import_head")" = "commit"
sg_generator_root="/private/tmp/terraform-aws-security-groups-generator/task2-$sg_import_head"
sg_hcl_worklist="$sg_generator_root/module-hcl-files"
sg_hcl_pristine_manifest="$sg_generator_root/module-hcl-pristine"
sg_hcl_before_manifest="$sg_verification_root/module-hcl-before"
sg_hcl_after_manifest="$sg_verification_root/module-hcl-after"
sg_docs_files="$sg_verification_root/docs-files"
sg_docs_worklist="$sg_verification_root/docs-directories"
test -d "$sg_generator_root"
test -s "$sg_hcl_worklist"
test -s "$sg_hcl_pristine_manifest"
test "$(wc -l < "$sg_hcl_worklist" | tr -d '[:space:]')" = "212"
test "$(wc -l < "$sg_hcl_pristine_manifest" | tr -d '[:space:]')" = "212"
find modules -type f -name '*.tf' | sort \
  > "$sg_verification_root/current-module-hcl-files"
cmp "$sg_hcl_worklist" "$sg_verification_root/current-module-hcl-files"
sg_hcl_manifest_count=0
while IFS= read -r sg_hcl_file; do
  test -n "$sg_hcl_file"
  sg_hcl_manifest_count=$((sg_hcl_manifest_count + 1))
  shasum -a 256 "$sg_hcl_file"
done < "$sg_hcl_worklist" > "$sg_hcl_before_manifest"
test "$sg_hcl_manifest_count" = "212"
test "$(wc -l < "$sg_hcl_before_manifest" | tr -d '[:space:]')" = "212"
cmp "$sg_hcl_pristine_manifest" "$sg_hcl_before_manifest"
terraform -chdir=generate init -backend=false -input=false
terraform -chdir=generate apply -auto-approve -input=false
sg_hcl_manifest_count=0
while IFS= read -r sg_hcl_file; do
  test -n "$sg_hcl_file"
  sg_hcl_manifest_count=$((sg_hcl_manifest_count + 1))
  shasum -a 256 "$sg_hcl_file"
done < "$sg_hcl_worklist" > "$sg_hcl_after_manifest"
test "$sg_hcl_manifest_count" = "212"
test "$(wc -l < "$sg_hcl_after_manifest" | tr -d '[:space:]')" = "212"
cmp "$sg_hcl_pristine_manifest" "$sg_hcl_after_manifest"
rg -l '<!-- BEGIN_TF_DOCS -->' -g README.md > "$sg_docs_files"
test "$(wc -l < "$sg_docs_files" | tr -d '[:space:]')" = "55"
while IFS= read -r sg_docs_file; do
  dirname "$sg_docs_file"
done < "$sg_docs_files" | sort -u > "$sg_docs_worklist"
test "$(wc -l < "$sg_docs_worklist" | tr -d '[:space:]')" = "55"
while IFS= read -r sg_docs_dir; do
  go run github.com/terraform-docs/terraform-docs@v0.24.0 markdown table \
    --lockfile=false --output-file README.md --output-mode inject "$sg_docs_dir"
done < "$sg_docs_worklist"
git diff --exit-code
terraform fmt -check -recursive
```

Expected: the uniquely identified import commit resolves the deterministic Task
2 state, the current and post-apply 212-file manifests both equal the retained
pre-first-apply manifest, docs are stable after regeneration, and formatting
passes.

- [ ] **Step 2: Run inherited TFLint rules**

```bash
set -euo pipefail
sg_tflint_tmp=$(mktemp -d)
curl -fsSL https://github.com/terraform-linters/tflint/releases/download/v0.62.0/tflint_darwin_arm64.zip -o "$sg_tflint_tmp/tflint.zip"
unzip -q "$sg_tflint_tmp/tflint.zip" -d "$sg_tflint_tmp"
"$sg_tflint_tmp/tflint" --recursive \
  --only=terraform_deprecated_interpolation --only=terraform_deprecated_index \
  --only=terraform_unused_declarations --only=terraform_comment_syntax \
  --only=terraform_documented_outputs --only=terraform_documented_variables \
  --only=terraform_typed_variables --only=terraform_module_pinned_source \
  --only=terraform_naming_convention --only=terraform_required_version \
  --only=terraform_required_providers --only=terraform_standard_module_structure \
  --only=terraform_workspace_remote
```

- [ ] **Step 3: Initialize and validate exactly 110 roots**

```bash
set -euo pipefail
sg_validation_root=$(mktemp -d /private/tmp/terraform-aws-security-groups-roots.XXXXXX)
sg_plugin_cache=$(mktemp -d)
sg_versions_files="$sg_validation_root/versions-files"
sg_tf_worklist="$sg_validation_root/terraform-directories"
rg --files -g versions.tf > "$sg_versions_files"
test "$(wc -l < "$sg_versions_files" | tr -d '[:space:]')" = "110"
while IFS= read -r sg_versions_file; do
  dirname "$sg_versions_file"
done < "$sg_versions_files" | sort -u > "$sg_tf_worklist"
sg_tf_worklist_count=$(wc -l < "$sg_tf_worklist" | tr -d '[:space:]')
case "$sg_tf_worklist_count" in
  ''|*[!0-9]*) exit 1 ;;
esac
test "$sg_tf_worklist_count" = "110"
sg_tf_directories=()
while IFS= read -r sg_tf_dir; do
  test -n "$sg_tf_dir"
  sg_tf_directories[${#sg_tf_directories[@]}]="$sg_tf_dir"
done < "$sg_tf_worklist"
test "${#sg_tf_directories[@]}" -eq "$sg_tf_worklist_count"
sg_root_count=0
for sg_tf_dir in "${sg_tf_directories[@]}"; do
  sg_root_count=$((sg_root_count + 1))
  printf 'Validating %s\n' "$sg_tf_dir"
  TF_PLUGIN_CACHE_DIR="$sg_plugin_cache" terraform -chdir="$sg_tf_dir" init -backend=false -input=false
  TF_PLUGIN_CACHE_DIR="$sg_plugin_cache" terraform -chdir="$sg_tf_dir" validate
done
test "$sg_root_count" = "110"
```

Expected: root, Complete example, generator, 53 modules, and 54 wrappers validate without AWS operations.

- [ ] **Step 4: Prove full HCL parity from a fresh archived reference**

```bash
set -euo pipefail
sg_expected_sha='58d8e895915f5573767081142d063b7caf7a2b47'
sg_compare_root=$(mktemp -d /private/tmp/terraform-aws-security-group-compare.XXXXXX)
sg_verified_clone="$sg_compare_root/verified-upstream"
sg_pristine_root="$sg_compare_root/pristine"
git clone --quiet --depth 1 --branch v6.0.0 \
  https://github.com/terraform-aws-modules/terraform-aws-security-group.git \
  "$sg_verified_clone"
test "$(git -C "$sg_verified_clone" rev-parse HEAD)" = "$sg_expected_sha"
test "$(git -C "$sg_verified_clone" rev-parse refs/tags/v6.0.0^{commit})" = \
  "$sg_expected_sha"
test -z "$(git -C "$sg_verified_clone" status --porcelain)"
mkdir "$sg_pristine_root"
git -C "$sg_verified_clone" archive --format=tar HEAD | \
  tar -xf - -C "$sg_pristine_root"
test ! -e "$sg_pristine_root/.git"
sg_tf_worklist="$sg_compare_root/terraform-files"
find "$sg_pristine_root" -type f -name '*.tf' -print | \
  sed "s#^$sg_pristine_root/##" | sort > "$sg_tf_worklist"
sg_tf_count=$(wc -l < "$sg_tf_worklist" | tr -d '[:space:]')
case "$sg_tf_count" in
  ''|*[!0-9]*) exit 1 ;;
esac
test "$sg_tf_count" = "441"
sg_compared_tf_count=0
while IFS= read -r sg_tf_file; do
  test -n "$sg_tf_file"
  sg_compared_tf_count=$((sg_compared_tf_count + 1))
  case "$sg_tf_file" in
    main.tf|variables.tf|wrappers/main.tf) continue ;;
  esac
  cmp "$sg_pristine_root/$sg_tf_file" "$sg_tf_file"
done < "$sg_tf_worklist"
test "$sg_compared_tf_count" -eq "$sg_tf_count"
sg_hcl_notice='# Modified by joeroberts/terraform-aws-security-groups on 2026-08-12; see UPSTREAM.md.'
for sg_hcl_file in main.tf variables.tf wrappers/main.tf; do
  test "$(head -n 1 "$sg_hcl_file")" = "$sg_hcl_notice"
done
perl -pe 's/create = var\.create && var\.[A-Za-z0-9_]+/create = var.create/' \
  "$sg_pristine_root/main.tf" > "$sg_compare_root/expected-main.tf"
sed '1d' main.tf > "$sg_compare_root/actual-main.tf"
cmp "$sg_compare_root/expected-main.tf" "$sg_compare_root/actual-main.tf"
sed '140,144d' "$sg_pristine_root/variables.tf" \
  > "$sg_compare_root/expected-variables.tf"
sed '1d' variables.tf > "$sg_compare_root/actual-variables.tf"
cmp "$sg_compare_root/expected-variables.tf" \
  "$sg_compare_root/actual-variables.tf"
sed '12d' "$sg_pristine_root/wrappers/main.tf" \
  > "$sg_compare_root/expected-wrappers-main.tf"
sed '1d' wrappers/main.tf > "$sg_compare_root/actual-wrappers-main.tf"
cmp "$sg_compare_root/expected-wrappers-main.tf" \
  "$sg_compare_root/actual-wrappers-main.tf"
sg_create_matches="$sg_compare_root/direct-create-matches"
rg -n '^  create = var\.create$' main.tf > "$sg_create_matches"
test "$(wc -l < "$sg_create_matches" | tr -d '[:space:]')" = "1"
{
  printf '%s\n' \
    '<!-- Modified by joeroberts/terraform-aws-security-groups on 2026-08-12; see UPSTREAM.md. -->'
  sed '194d' "$sg_pristine_root/CHANGELOG.md"
} > "$sg_compare_root/expected-CHANGELOG.md"
cmp "$sg_compare_root/expected-CHANGELOG.md" CHANGELOG.md
```

Expected: every unchanged Terraform file is byte-identical, each of the three
approved HCL files matches its deterministic transform, the direct creation
expression occurs once, and `CHANGELOG.md` remains the exact authorized
bullet-deletion-plus-notice transform. This fence creates and verifies its own
fresh clone and archives its committed tree; it does not consume Task 1 shell
state or import upstream Git history.

- [ ] **Step 5: Notices, workflows, neutrality/history, and remote synchronization**

```bash
set -euo pipefail
sg_final_gate=$(mktemp -d /private/tmp/terraform-aws-security-groups-final.XXXXXX)
sg_notice='Modified by joeroberts/terraform-aws-security-groups on 2026-08-12; see UPSTREAM.md.'
for sg_notice_file in main.tf variables.tf wrappers/main.tf README.md CHANGELOG.md \
  generate/templates/README.md.tftpl modules/*/README.md wrappers/README.md \
  wrappers/*/README.md .github/workflows/*; do
  case "$sg_notice_file" in
    *.tf|.github/workflows/*) sg_expected_notice="# $sg_notice" ;;
    *.md|*.tftpl) sg_expected_notice="<!-- $sg_notice -->" ;;
    *) exit 1 ;;
  esac
  test "$(head -n 1 "$sg_notice_file")" = "$sg_expected_notice"
done
if rg -n 'source\s*=\s*"(terraform-aws-modules/security-group/aws|tfr:///terraform-aws-modules/security-group/aws)' \
  README.md modules wrappers -g README.md; then
  exit 1
else
  sg_rg_status=$?
  test "$sg_rg_status" = "1"
fi
if rg -n -P 'uses:\s+[^\s#]+@(?![0-9a-f]{40}(?:\s|$))' .github/workflows; then
  exit 1
else
  sg_rg_status=$?
  test "$sg_rg_status" = "1"
fi
sg_workflow_files="$sg_final_gate/workflow-files"
find .github/workflows -maxdepth 1 -type f | sort > "$sg_workflow_files"
test "$(wc -l < "$sg_workflow_files" | tr -d '[:space:]')" = "6"
sed -nE 's/^[[:space:]]*-[[:space:]]*(uses:.*)$/\1/p; s/^[[:space:]]+(uses:.*)$/\1/p' \
  .github/workflows/* > "$sg_final_gate/all-actions"
test "$(wc -l < "$sg_final_gate/all-actions" | tr -d '[:space:]')" = "19"
sg_require_action() {
  sg_required_action=$1
  sg_required_count=$2
  sg_action_matches="$sg_final_gate/action-matches"
  rg -Fx "$sg_required_action" "$sg_final_gate/all-actions" \
    > "$sg_action_matches"
  test "$(wc -l < "$sg_action_matches" | tr -d '[:space:]')" = \
    "$sg_required_count"
}
sg_require_action \
  'uses: actions/checkout@fbc6f3992d24b796d5a048ff273f7fcc4a7b6c09 # v5' 5
sg_require_action \
  'uses: actions/setup-node@249970729cb0ef3589644e2896645e5dc5ba9c38 # v6' 1
sg_require_action \
  'uses: actions/stale@1e223db275d687790206a7acac4d1a11bd6fe629 # v10' 1
sg_require_action \
  'uses: amannn/action-semantic-pull-request@48f256284bd46cdaab1048c3721360e808335d50 # v6.1.1' 1
sg_require_action \
  'uses: clowdhaus/terraform-composite-actions/directories@462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0' 1
sg_require_action \
  'uses: clowdhaus/terraform-composite-actions/pre-commit@462243b714d762cbcac6732098e9fdb4ab236cb7 # v1.14.0' 3
sg_require_action \
  'uses: clowdhaus/terraform-min-max@a86951cbe89f4d15caec805f36aa1dd68863ae32 # v2.1.0' 2
sg_require_action \
  'uses: cycjimmy/semantic-release-action@ba330626c4750c19d8299de843f05c7aa5574f62 # v5 branch; tag v5.0.2' 1
sg_require_action \
  'uses: dessant/lock-threads@1bf7ec25051fe7c00bdd17e6a7cf3d7bfb7dc771 # v5' 1
sg_require_action \
  'uses: hashicorp/setup-terraform@b9cd54a3c349d3f38e8881555d616ced269862dd # v3' 1
sg_require_action \
  'uses: jaxxstorm/action-install-gh-release@6096f2a2bbfee498ced520b6922ac2c06e990ed2 # v2.1.0' 2
while IFS= read -r sg_workflow_file; do
  test "$(rg -c '^permissions:$' "$sg_workflow_file")" = "1"
  awk '
    $0 == "permissions:" { capture = 1; print; next }
    capture && /^  [a-z-]+: (read|write)$/ { print; next }
    capture { exit }
  ' "$sg_workflow_file" > "$sg_final_gate/actual-permissions"
  case "$(basename "$sg_workflow_file")" in
    generate-modules.yml|pre-commit.yml|release.yml)
      printf '%s\n' 'permissions:' '  contents: read' \
        > "$sg_final_gate/expected-permissions"
      ;;
    pr-title.yml)
      printf '%s\n' 'permissions:' '  pull-requests: read' \
        > "$sg_final_gate/expected-permissions"
      ;;
    lock.yml|stale-actions.yaml)
      printf '%s\n' 'permissions:' '  issues: write' '  pull-requests: write' \
        > "$sg_final_gate/expected-permissions"
      ;;
    *) exit 1 ;;
  esac
  cmp "$sg_final_gate/expected-permissions" \
    "$sg_final_gate/actual-permissions"
done < "$sg_workflow_files"
rg -Fxq "    if: github.repository_owner == 'terraform-aws-modules'" \
  .github/workflows/release.yml
go run github.com/rhysd/actionlint/cmd/actionlint@v1.7.7
sg_neutral_pattern="$(printf '%s|%s|%s|%s|%s|%s|%s' \
  'put''in' 'khuy''lo' 'ukr''ain' 'russ''ia' 'bela''rus' 'cri''mea' 'don''bas')"
sg_scan_worklist="$sg_final_gate/scan-files"
find . \( -path './.git' -o -path './.superpowers' \) -prune -o \
  -type d -name .terraform -prune -o -type f -print0 > "$sg_scan_worklist"
test -s "$sg_scan_worklist"
sg_tree_scan_status=0
while IFS= read -r -d '' sg_scan_file; do
  if rg -n -i "$sg_neutral_pattern" -- "$sg_scan_file"; then
    sg_tree_scan_status=1
  else
    sg_rg_status=$?
    test "$sg_rg_status" = "1"
  fi
done < "$sg_scan_worklist"
test "$sg_tree_scan_status" = "0"
sg_revisions="$sg_final_gate/revisions"
git rev-list --all > "$sg_revisions"
sg_revision_line_count=$(wc -l < "$sg_revisions" | tr -d '[:space:]')
case "$sg_revision_line_count" in
  ''|*[!0-9]*) exit 1 ;;
esac
test "$sg_revision_line_count" -gt "0"
sg_revision_args=()
while IFS= read -r sg_revision; do
  test -n "$sg_revision"
  sg_revision_args[${#sg_revision_args[@]}]="$sg_revision"
done < "$sg_revisions"
test "${#sg_revision_args[@]}" -eq "$sg_revision_line_count"
if git grep -nEi "$sg_neutral_pattern" "${sg_revision_args[@]}"; then
  exit 1
else
  sg_git_grep_status=$?
  test "$sg_git_grep_status" = "1"
fi
git diff --check
test -z "$(git status --porcelain)"
test -z "$(git ls-files .superpowers)"
git check-ignore -q \
  .superpowers/sdd/2026-08-12-security-groups-neutral-derivative/plan-amendment-report.md
git fetch origin neutral/v6.0.0-neutral.1
test "$(git rev-parse HEAD)" = "$(git rev-parse origin/neutral/v6.0.0-neutral.1)"
git ls-remote --tags origin refs/tags/v6.0.0-neutral.1 \
  > "$sg_final_gate/reserved-tag"
test ! -s "$sg_final_gate/reserved-tag"
```

---

### Task 5: Independent Review, PR, and Campaign Status

**Files:**
- Modify only for findings: Tasks 1-3 files
- Modify cross-repository: IAM `docs/neutralization/CAMPAIGN-STATUS.md`
- External write: GitHub PR

**Interfaces:**
- Consumes: Task 4 evidence
- Produces: reviewed Security Groups PR and persisted campaign milestone

- [ ] **Step 1: Obtain fresh requirements and code-quality reviews**

Use `superpowers:requesting-code-review`. Require checks of the singular-to-plural
provenance mapping, three-file HCL delta, 53-entry generator, 108 consumer docs,
110 validations, Apache notices, action pins, history neutrality, and tag deferral.
Fix, push, rerun Task 4, and obtain a fresh re-review until no load-bearing finding remains.

- [ ] **Step 2: Create and read back the PR**

Prepare `/private/tmp/terraform-aws-security-groups-pr-body.md` with provenance,
intentional changes, generator evidence, validation counts, independent review,
and deferred tag. Run:

```bash
set -euo pipefail
sg_branch='neutral/v6.0.0-neutral.1'
sg_repo='joeroberts/terraform-aws-security-groups'
sg_pr_body='/private/tmp/terraform-aws-security-groups-pr-body.md'
sg_pr_gate=$(mktemp -d /private/tmp/terraform-aws-security-groups-pr.XXXXXX)
test "$(git branch --show-current)" = "$sg_branch"
test "$(git remote get-url origin)" = \
  'git@github.com:joeroberts/terraform-aws-security-groups.git'
test -z "$(git status --porcelain)"
git fetch origin "$sg_branch"
sg_verified_head=$(git rev-parse HEAD)
printf '%s\n' "$sg_verified_head" > "$sg_pr_gate/verified-head"
test "$sg_verified_head" = "$(git rev-parse '@{upstream}')"
test "$sg_verified_head" = "$(git rev-parse "origin/$sg_branch")"
git merge-base --is-ancestor HEAD "origin/$sg_branch"
git merge-base --is-ancestor "origin/$sg_branch" HEAD
git tag -l v6.0.0-neutral.1 > "$sg_pr_gate/local-reserved-tag"
test ! -s "$sg_pr_gate/local-reserved-tag"
git ls-remote --tags origin refs/tags/v6.0.0-neutral.1 \
  > "$sg_pr_gate/remote-reserved-tag"
test ! -s "$sg_pr_gate/remote-reserved-tag"
test -s "$sg_pr_body"
gh pr list --repo "$sg_repo" --head "$sg_branch" --state all \
  --json number --jq 'length' > "$sg_pr_gate/preexisting-count"
IFS= read -r sg_preexisting_count < "$sg_pr_gate/preexisting-count"
test "$sg_preexisting_count" = "0"
gh pr create --repo "$sg_repo" \
  --base main --head neutral/v6.0.0-neutral.1 \
  --title "feat: add neutral security group module v6.0.0" \
  --body-file "$sg_pr_body" > "$sg_pr_gate/create-output"
rg -o 'https://github\.com/joeroberts/terraform-aws-security-groups/pull/[0-9]+' \
  "$sg_pr_gate/create-output" > "$sg_pr_gate/created-url"
test "$(wc -l < "$sg_pr_gate/created-url" | tr -d '[:space:]')" = "1"
IFS= read -r sg_pr_url < "$sg_pr_gate/created-url"
printf '%s\n' "$sg_pr_url" | sed -nE 's#^.*/pull/([0-9]+)$#\1#p' \
  > "$sg_pr_gate/created-number"
test "$(wc -l < "$sg_pr_gate/created-number" | tr -d '[:space:]')" = "1"
IFS= read -r sg_pr_number < "$sg_pr_gate/created-number"
case "$sg_pr_number" in
  ''|*[!0-9]*) exit 1 ;;
esac
gh pr view "$sg_pr_number" --repo "$sg_repo" \
  --json url,number,state,baseRefName,headRefName,commits \
  --jq '[.url, (.number|tostring), .state, .baseRefName, .headRefName, .commits[-1].oid] | @tsv' \
  > "$sg_pr_gate/readback"
test "$(wc -l < "$sg_pr_gate/readback" | tr -d '[:space:]')" = "1"
IFS=$'\t' read -r sg_read_url sg_read_number sg_read_state sg_read_base \
  sg_read_head sg_read_oid < "$sg_pr_gate/readback"
test "$sg_read_url" = "$sg_pr_url"
test "$sg_read_url" = \
  "https://github.com/joeroberts/terraform-aws-security-groups/pull/$sg_pr_number"
test "$sg_read_number" = "$sg_pr_number"
test "$sg_read_state" = "OPEN"
test "$sg_read_base" = "main"
test "$sg_read_head" = "$sg_branch"
test "$sg_read_oid" = "$sg_verified_head"
IFS= read -r sg_saved_verified_head < "$sg_pr_gate/verified-head"
test "$(git rev-parse HEAD)" = "$sg_saved_verified_head"
```

Expected: the exact synchronized and clean verified HEAD has no reserved tag;
the create response yields one exact PR URL/number; and readback by that number
(never contextual branch inference) proves an open `main` PR from the exact
neutral branch whose last commit is the same verified HEAD.

- [ ] **Step 3: Update the IAM campaign journal and continue**

Resolve and print the exact cross-repository journal line from explicit
Security Groups PR evidence while proving the known IAM breaker state:

```bash
set -euo pipefail
iam_worktree='/Users/jroberts/Documents/dev/joeroberts/terraform/.worktrees/terraform-aws-iam/v6.8.0-neutral.1'
iam_repo='joeroberts/terraform-aws-iam'
iam_branch='neutral/v6.8.0-neutral.1'
iam_expected_head='0c3f4f5af53e89e6f7ee477d80ebe65283e43014'
iam_journal="$iam_worktree/docs/neutralization/CAMPAIGN-STATUS.md"
sg_repo='joeroberts/terraform-aws-security-groups'
sg_branch='neutral/v6.0.0-neutral.1'
iam_gate=$(mktemp -d /private/tmp/terraform-aws-iam-sg-journal.XXXXXX)
test -d "$iam_worktree"
test -f "$iam_journal"
test "$(git -C "$iam_worktree" branch --show-current)" = "$iam_branch"
test "$(git -C "$iam_worktree" remote get-url origin)" = \
  'git@github.com:joeroberts/terraform-aws-iam.git'
test "$(git -C "$iam_worktree" rev-parse HEAD)" = "$iam_expected_head"
test "$(git -C "$iam_worktree" log -1 --format=%s)" = \
  'docs: record IAM neutralization breaker'
test -z "$(git -C "$iam_worktree" status --porcelain)"
git -C "$iam_worktree" fetch origin "$iam_branch"
test "$iam_expected_head" = \
  "$(git -C "$iam_worktree" rev-parse "origin/$iam_branch")"
rg -Fxq '# IAM neutral derivative execution blocker' \
  "$iam_worktree/docs/superpowers/status/2026-08-12-iam-neutral-derivative-blocker.md"
rg -Fxq -- '- IAM: blocked after the five-round amendment breaker; see [BLOCKER.md](BLOCKER.md).' \
  "$iam_journal"
rg -Fxq -- '- IAM has no PR, tag, or release.' "$iam_journal"
gh pr list --repo "$sg_repo" --head "$sg_branch" --state open \
  --json number --jq '.[].number' > "$iam_gate/sg-pr-numbers"
test "$(wc -l < "$iam_gate/sg-pr-numbers" | tr -d '[:space:]')" = "1"
IFS= read -r sg_pr_number < "$iam_gate/sg-pr-numbers"
case "$sg_pr_number" in
  ''|*[!0-9]*) exit 1 ;;
esac
gh pr view "$sg_pr_number" --repo "$sg_repo" \
  --json url,state,baseRefName,headRefName,commits \
  --jq '[.url, .state, .baseRefName, .headRefName, .commits[-1].oid] | @tsv' \
  > "$iam_gate/sg-pr-readback"
test "$(wc -l < "$iam_gate/sg-pr-readback" | tr -d '[:space:]')" = "1"
IFS=$'\t' read -r sg_pr_url sg_pr_state sg_pr_base sg_pr_head sg_pr_oid \
  < "$iam_gate/sg-pr-readback"
test "$sg_pr_state" = "OPEN"
test "$sg_pr_base" = "main"
test "$sg_pr_head" = "$sg_branch"
test -n "$sg_pr_oid"
printf '%s\n' \
  "- Security Groups: PR open — branch \`$sg_branch\`; commit \`$sg_pr_oid\`; PR [#$sg_pr_number]($sg_pr_url); generator HCL stable; 110 Terraform roots validated; tag deferred." \
  > "$iam_gate/expected-journal-line"
printf 'APPLY_PATCH_TARGET=%s\n' "$iam_journal"
printf '%s' 'SECURITY_GROUPS_JOURNAL_LINE='
sed -n '1p' "$iam_gate/expected-journal-line"
```

Use `apply_patch` with the printed literal absolute target
`/Users/jroberts/Documents/dev/joeroberts/terraform/.worktrees/terraform-aws-iam/v6.8.0-neutral.1/docs/neutralization/CAMPAIGN-STATUS.md`.
Add the printed Security Groups line, change the exact `Next` line to
`- Next: RDS.`, and preserve both IAM blocker statements verbatim; do not claim
that IAM is implemented or has a PR.

Then verify and publish only that IAM journal mutation, with every repository
operation explicitly rooted:

```bash
set -euo pipefail
iam_worktree='/Users/jroberts/Documents/dev/joeroberts/terraform/.worktrees/terraform-aws-iam/v6.8.0-neutral.1'
iam_repo='joeroberts/terraform-aws-iam'
iam_branch='neutral/v6.8.0-neutral.1'
iam_expected_head='0c3f4f5af53e89e6f7ee477d80ebe65283e43014'
iam_journal_rel='docs/neutralization/CAMPAIGN-STATUS.md'
iam_journal="$iam_worktree/$iam_journal_rel"
sg_repo='joeroberts/terraform-aws-security-groups'
sg_branch='neutral/v6.0.0-neutral.1'
iam_gate=$(mktemp -d /private/tmp/terraform-aws-iam-sg-journal.XXXXXX)
test "$(git -C "$iam_worktree" branch --show-current)" = "$iam_branch"
test "$(git -C "$iam_worktree" remote get-url origin)" = \
  'git@github.com:joeroberts/terraform-aws-iam.git'
test "$(git -C "$iam_worktree" rev-parse HEAD)" = "$iam_expected_head"
gh pr list --repo "$sg_repo" --head "$sg_branch" --state open \
  --json number --jq '.[].number' > "$iam_gate/sg-pr-numbers"
test "$(wc -l < "$iam_gate/sg-pr-numbers" | tr -d '[:space:]')" = "1"
IFS= read -r sg_pr_number < "$iam_gate/sg-pr-numbers"
gh pr view "$sg_pr_number" --repo "$sg_repo" \
  --json url,state,baseRefName,headRefName,commits \
  --jq '[.url, .state, .baseRefName, .headRefName, .commits[-1].oid] | @tsv' \
  > "$iam_gate/sg-pr-readback"
IFS=$'\t' read -r sg_pr_url sg_pr_state sg_pr_base sg_pr_head sg_pr_oid \
  < "$iam_gate/sg-pr-readback"
test "$sg_pr_state" = "OPEN"
test "$sg_pr_base" = "main"
test "$sg_pr_head" = "$sg_branch"
printf '%s\n' \
  "- Security Groups: PR open — branch \`$sg_branch\`; commit \`$sg_pr_oid\`; PR [#$sg_pr_number]($sg_pr_url); generator HCL stable; 110 Terraform roots validated; tag deferred." \
  > "$iam_gate/expected-journal-line"
rg -Fxq -f "$iam_gate/expected-journal-line" "$iam_journal"
rg -Fxq -- '- IAM: blocked after the five-round amendment breaker; see [BLOCKER.md](BLOCKER.md).' \
  "$iam_journal"
rg -Fxq -- '- IAM has no PR, tag, or release.' "$iam_journal"
rg -Fxq -- '- Next: RDS.' "$iam_journal"
if rg -n '^- IAM:.*(PR open|implemented|complete)' "$iam_journal"; then
  exit 1
else
  iam_rg_status=$?
  test "$iam_rg_status" = "1"
fi
git -C "$iam_worktree" diff --name-only > "$iam_gate/changed-paths"
test "$(wc -l < "$iam_gate/changed-paths" | tr -d '[:space:]')" = "1"
rg -Fxq "$iam_journal_rel" "$iam_gate/changed-paths"
git -C "$iam_worktree" diff --check -- "$iam_journal_rel"
git -C "$iam_worktree" add -- "$iam_journal_rel"
git -C "$iam_worktree" diff --cached --name-only \
  > "$iam_gate/staged-paths"
test "$(wc -l < "$iam_gate/staged-paths" | tr -d '[:space:]')" = "1"
rg -Fxq "$iam_journal_rel" "$iam_gate/staged-paths"
git -C "$iam_worktree" commit -m \
  'docs: record Security Groups campaign milestone'
git -C "$iam_worktree" push origin "HEAD:refs/heads/$iam_branch"
git -C "$iam_worktree" fetch origin "$iam_branch"
test "$(git -C "$iam_worktree" rev-parse HEAD)" = \
  "$(git -C "$iam_worktree" rev-parse "origin/$iam_branch")"
test -z "$(git -C "$iam_worktree" status --porcelain)"
```

Expected: only the explicit IAM campaign journal is committed and normally
pushed in the IAM repository. IAM remains accurately recorded as blocked with
no PR/tag/release, Security Groups is recorded from numbered PR readback, and
execution may proceed to RDS without any command implicitly targeting the
Security Groups worktree.
