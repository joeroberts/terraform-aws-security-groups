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
- Upstream v6.0.0 contains no `*.tftest.hcl`; creation assertions, generator parity, full HCL parity, and 110-root init/validate are the focused behavioral tests.
- Never import upstream Git history or copy an unsanitized snapshot into the target.
- Push each milestone without force. If blocked, persist and push `docs/neutralization/BLOCKER.md`, open a draft PR when coherent, update the IAM campaign journal, and proceed to RDS.

## File Map

- Import: complete upstream v6.0.0 working tree except `.git/`.
- Modify before copy: `main.tf:2`, `variables.tf:140-144`, `wrappers/main.tf:12`, `README.md:5,130,158-162`.
- Modify after copy: `generate/templates/README.md.tftpl` — derivative Git source, no Registry version argument, local license, modification notice.
- Regenerate after template change: all 53 `modules/*/README.md`.
- Modify mechanically after copy: root `README.md` and all 54 `wrappers/**/README.md` files — derivative identity, sources, notices.
- Modify: six `.github/workflows/*` files — notices, action pins, permissions.
- Create: `UPSTREAM.md`.
- Preserve unchanged: `generate/catalog.tf`, other templates, all 53 preset-module HCL outputs, and unrelated external VPC example source.

---

### Task 1: Sanitized Root Import

**Files:**
- Modify: `main.tf`, `variables.tf`, `wrappers/main.tf`, `README.md`
- Create: `UPSTREAM.md`
- Import: remaining upstream tree

**Interfaces:**
- Consumes: upstream v6.0.0 and target license-only main
- Produces: neutral root module controlled solely by `var.create`

- [ ] **Step 1: Verify branch and exact upstream release**

```bash
test "$(git branch --show-current)" = "neutral/v6.0.0-neutral.1"
test "$(git remote get-url origin)" = "git@github.com:joeroberts/terraform-aws-security-groups.git"
test -z "$(git status --porcelain)"
sg_import_root=$(mktemp -d /private/tmp/terraform-aws-security-group-v6.0.0.XXXXXX)
git clone --quiet --depth 1 --branch v6.0.0 \
  https://github.com/terraform-aws-modules/terraform-aws-security-group.git \
  "$sg_import_root/source"
test "$(git -C "$sg_import_root/source" rev-parse HEAD)" = \
  "58d8e895915f5573767081142d063b7caf7a2b47"
```

- [ ] **Step 2: Prove pristine acceptance fails**

```bash
sg_neutral_pattern="$(printf '%s|%s|%s|%s|%s|%s|%s' \
  'put''in' 'khuy''lo' 'ukr''ain' 'russ''ia' 'bela''rus' 'cri''mea' 'don''bas')"
test -n "$(rg -l -i "$sg_neutral_pattern" "$sg_import_root/source" \
  --hidden --glob '!.git/**')"
if rg -n '^  create = var\.create$' "$sg_import_root/source/main.tf"; then exit 1; fi
```

Expected: forbidden matches exist and the desired direct expression does not.

- [ ] **Step 3: Apply minimal neutralization before copy**

With `apply_patch` under `$sg_import_root/source`:

- Change `main.tf:2` to `create = var.create`.
- Delete the complete variable block at `variables.tf:140-144`.
- Delete `wrappers/main.tf:12`.
- Delete `README.md:5` and final section `README.md:158-162`.
- Regenerate the root input table, removing pristine row 130:

```bash
go run github.com/terraform-docs/terraform-docs@v0.24.0 markdown table \
  --lockfile=false --output-file README.md --output-mode inject \
  "$sg_import_root/source"
```

Prepend the dated HCL notice to the three changed `.tf` files and the dated HTML
notice to `README.md`. Then run:

```bash
rg -n '^  create = var\.create$' "$sg_import_root/source/main.tf"
if rg -n -i "$sg_neutral_pattern" "$sg_import_root/source" \
  --hidden --glob '!.git/**'; then exit 1; fi
```

- [ ] **Step 4: Copy the sanitized tree and create provenance**

```bash
rsync -a --exclude .git "$sg_import_root/source/" ./
test -f generate/catalog.tf
test "$(find modules -mindepth 1 -maxdepth 1 -type d | wc -l | tr -d ' ')" = "53"
test "$(find wrappers -mindepth 1 -maxdepth 1 -type d | wc -l | tr -d ' ')" = "53"
```

Create `UPSTREAM.md` with the singular upstream URL, exact tag/SHA/import date,
plural target name, reserved neutral tag, intentional input/gate/wrapper/docs
deltas, unchanged-default statement, Apache notices, upstream authorship versus
derivative maintenance, catalog ownership, and an update process that sanitizes
before copy and never merges upstream history.

- [ ] **Step 5: Prove approved HCL delta and commit**

```bash
diff -u \
  <(git -C "$sg_import_root/source" show HEAD:main.tf | perl -pe 's/create = var\.create && var\.[A-Za-z0-9_]+/create = var.create/') \
  <(sed '1d' main.tf)
diff -u \
  <(git -C "$sg_import_root/source" show HEAD:variables.tf | sed '140,144d' | perl -0pe 's/\n+\z/\n/') \
  <(sed '1d' variables.tf | perl -0pe 's/\n+\z/\n/')
diff -u \
  <(git -C "$sg_import_root/source" show HEAD:wrappers/main.tf | sed '12d') \
  <(sed '1d' wrappers/main.tf)
terraform fmt -check -recursive
git diff --check
git add . ':!docs/superpowers'
git commit -m "feat: import neutral security group module v6.0.0"
git push -u origin neutral/v6.0.0-neutral.1
```

Expected: three exact HCL changes plus notices, all imported in one neutral commit.

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
test "$(rg -l 'terraform-aws-modules/security-group/aws|tfr:///terraform-aws-modules/security-group/aws' \
  README.md modules wrappers -g README.md | wc -l | tr -d ' ')" = "108"
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
terraform -chdir=generate init -backend=false -input=false
terraform -chdir=generate apply -auto-approve -input=false
while IFS= read -r sg_docs_dir; do
  go run github.com/terraform-docs/terraform-docs@v0.24.0 markdown table \
    --lockfile=false --output-file README.md --output-mode inject "$sg_docs_dir"
done < <(rg -l '<!-- BEGIN_TF_DOCS -->' -g README.md | xargs -n1 dirname | sort -u)
```

Expected: exactly 53 module READMEs are regenerated from the modified template; generated HCL remains unchanged.

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
while IFS= read -r sg_wrapper_readme; do
  sg_wrapper_path=$(dirname "$sg_wrapper_readme")
  sg_wrapper_source="git::ssh://git@github.com/joeroberts/terraform-aws-security-groups.git//$sg_wrapper_path?ref=v6.0.0-neutral.1"
  printf '%s -> %s\n' "$sg_wrapper_readme" "$sg_wrapper_source"
done < <(find wrappers -name README.md -type f | sort)
```

Prepend the dated HTML notice to every wrapper README. This is an approved bulk
mechanical rewrite; do not change wrapper prose or HCL inputs.

- [ ] **Step 5: Prove generated HCL and consumer docs are correct**

```bash
sg_hcl_before=$(find modules -type f -name '*.tf' -print0 | sort -z | xargs -0 shasum -a 256 | shasum -a 256)
terraform -chdir=generate apply -auto-approve -input=false
test "$sg_hcl_before" = "$(find modules -type f -name '*.tf' -print0 | sort -z | xargs -0 shasum -a 256 | shasum -a 256)"
while IFS= read -r sg_docs_dir; do
  go run github.com/terraform-docs/terraform-docs@v0.24.0 markdown table \
    --lockfile=false --output-file README.md --output-mode inject "$sg_docs_dir"
done < <(rg -l '<!-- BEGIN_TF_DOCS -->' -g README.md | xargs -n1 dirname | sort -u)
if rg -n 'source\s*=\s*"(terraform-aws-modules/security-group/aws|tfr:///terraform-aws-modules/security-group/aws)' \
  README.md modules wrappers -g README.md; then exit 1; fi
test "$(rg -l 'v6\.0\.0-neutral\.1' README.md modules wrappers -g README.md | wc -l | tr -d ' ')" = "108"
```

Expected: generator changes no module HCL, all 108 consumer documents use the reserved neutral source, and docs are re-injected after generator execution.

- [ ] **Step 6: Commit and push documentation generation**

```bash
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
test -n "$(rg -n -P 'uses:\s+[^\s#]+@(?![0-9a-f]{40}(?:\s|$))' .github/workflows)"
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
if rg -n -P 'uses:\s+[^\s#]+@(?![0-9a-f]{40}(?:\s|$))' .github/workflows; then exit 1; fi
test "$(rg -l '^permissions:' .github/workflows | wc -l | tr -d ' ')" = "6"
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
sg_hcl_before=$(find modules -type f -name '*.tf' -print0 | sort -z | xargs -0 shasum -a 256 | shasum -a 256)
terraform -chdir=generate init -backend=false -input=false
terraform -chdir=generate apply -auto-approve -input=false
test "$sg_hcl_before" = "$(find modules -type f -name '*.tf' -print0 | sort -z | xargs -0 shasum -a 256 | shasum -a 256)"
while IFS= read -r sg_docs_dir; do
  go run github.com/terraform-docs/terraform-docs@v0.24.0 markdown table \
    --lockfile=false --output-file README.md --output-mode inject "$sg_docs_dir"
done < <(rg -l '<!-- BEGIN_TF_DOCS -->' -g README.md | xargs -n1 dirname | sort -u)
git diff --exit-code
terraform fmt -check -recursive
```

Expected: catalog regeneration changes no HCL, docs are stable after regeneration, and formatting passes.

- [ ] **Step 2: Run inherited TFLint rules**

```bash
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
sg_plugin_cache=$(mktemp -d)
sg_root_count=0
while IFS= read -r sg_tf_dir; do
  sg_root_count=$((sg_root_count + 1))
  printf 'Validating %s\n' "$sg_tf_dir"
  TF_PLUGIN_CACHE_DIR="$sg_plugin_cache" terraform -chdir="$sg_tf_dir" init -backend=false -input=false
  TF_PLUGIN_CACHE_DIR="$sg_plugin_cache" terraform -chdir="$sg_tf_dir" validate
done < <(rg --files -g versions.tf | xargs -n1 dirname | sort -u)
test "$sg_root_count" = "110"
```

Expected: root, Complete example, generator, 53 modules, and 54 wrappers validate without AWS operations.

- [ ] **Step 4: Prove full HCL parity and direct creation behavior**

```bash
sg_compare_root=$(mktemp -d /private/tmp/terraform-aws-security-group-compare.XXXXXX)
git clone --quiet --depth 1 --branch v6.0.0 \
  https://github.com/terraform-aws-modules/terraform-aws-security-group.git \
  "$sg_compare_root/upstream"
test "$(git -C "$sg_compare_root/upstream" rev-parse HEAD)" = \
  "58d8e895915f5573767081142d063b7caf7a2b47"
while IFS= read -r sg_tf_file; do
  case "$sg_tf_file" in
    main.tf|variables.tf|wrappers/main.tf) continue ;;
  esac
  diff -u "$sg_compare_root/upstream/$sg_tf_file" "$sg_tf_file"
done < <(git -C "$sg_compare_root/upstream" ls-files '*.tf' | sort)
diff -u \
  <(perl -pe 's/create = var\.create && var\.[A-Za-z0-9_]+/create = var.create/' "$sg_compare_root/upstream/main.tf") \
  <(sed '1d' main.tf)
diff -u \
  <(sed '140,144d' "$sg_compare_root/upstream/variables.tf" | perl -0pe 's/\n+\z/\n/') \
  <(sed '1d' variables.tf | perl -0pe 's/\n+\z/\n/')
diff -u \
  <(sed '12d' "$sg_compare_root/upstream/wrappers/main.tf") \
  <(sed '1d' wrappers/main.tf)
test "$(rg -c '^  create = var\.create$' main.tf)" = "1"
```

Expected: every unchanged Terraform file is byte-identical, each of the three
approved files matches its deterministic transform, and the direct creation
expression occurs once.

- [ ] **Step 5: Notices, workflows, neutrality/history, and remote synchronization**

```bash
sg_notice='Modified by joeroberts/terraform-aws-security-groups on 2026-08-12; see UPSTREAM.md.'
for sg_notice_file in main.tf variables.tf wrappers/main.tf README.md \
  generate/templates/README.md.tftpl modules/*/README.md wrappers/README.md \
  wrappers/*/README.md .github/workflows/*; do
  head -n 1 "$sg_notice_file" | rg -Fq "$sg_notice"
done
go run github.com/rhysd/actionlint/cmd/actionlint@v1.7.7
sg_neutral_pattern="$(printf '%s|%s|%s|%s|%s|%s|%s' \
  'put''in' 'khuy''lo' 'ukr''ain' 'russ''ia' 'bela''rus' 'cri''mea' 'don''bas')"
if rg -n -i "$sg_neutral_pattern" . --hidden --glob '!.git/**' --glob '!.terraform/**'; then exit 1; fi
if git grep -nEi "$sg_neutral_pattern" $(git rev-list --all); then exit 1; fi
git diff --check
test -z "$(git status --porcelain)"
git fetch origin neutral/v6.0.0-neutral.1
test "$(git rev-parse HEAD)" = "$(git rev-parse origin/neutral/v6.0.0-neutral.1)"
test -z "$(git ls-remote --tags origin refs/tags/v6.0.0-neutral.1)"
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
gh pr create --repo joeroberts/terraform-aws-security-groups \
  --base main --head neutral/v6.0.0-neutral.1 \
  --title "feat: add neutral security group module v6.0.0" \
  --body-file /private/tmp/terraform-aws-security-groups-pr-body.md
gh pr view --repo joeroberts/terraform-aws-security-groups \
  --json url,state,baseRefName,headRefName,commits,statusCheckRollup
```

- [ ] **Step 3: Update the IAM campaign journal and continue**

Record the PR URL, branch, final SHA, 110-root/generator evidence, and `tag
deferred` in the IAM journal. Commit `docs: record Security Groups campaign
milestone`, push the IAM neutral branch, then start the RDS plan without waiting
for merge.
