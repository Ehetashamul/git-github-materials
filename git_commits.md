For a **senior DevOps engineer**, you should know this as **Conventional Commits**. It is not just about making commit messages look clean—it can drive **versioning, release automation, changelogs, CI/CD rules, and auditability**.

## 1. The basic format

The most common format is:

```text
<type>[optional scope]: <description>
```

Examples:

```text
feat: add Azure VM deployment pipeline

fix: correct Terraform backend configuration

docs: update Kubernetes troubleshooting guide

refactor: simplify Docker build workflow

chore: upgrade GitHub Actions dependencies
```

You can also add a breaking-change marker:

```text
feat!: change deployment configuration format
```

or:

```text
feat(api)!: remove deprecated authentication endpoint
```

---

# 2. The most important commit types

| Type       | Meaning                                    | Example                                 |
| ---------- | ------------------------------------------ | --------------------------------------- |
| `feat`     | New functionality                          | `feat: add AKS deployment`              |
| `fix`      | Bug fix                                    | `fix: correct pipeline artifact path`   |
| `docs`     | Documentation only                         | `docs: update Terraform README`         |
| `refactor` | Code restructuring without behavior change | `refactor: simplify pipeline templates` |
| `test`     | Add/change tests                           | `test: add Terraform validation tests`  |
| `chore`    | Maintenance work                           | `chore: update dependency versions`     |
| `ci`       | CI/CD configuration                        | `ci: add security scan to pipeline`     |
| `build`    | Build system/dependencies                  | `build: update Docker base image`       |
| `perf`     | Performance improvement                    | `perf: optimize container build cache`  |
| `style`    | Formatting/style only                      | `style: format YAML files`              |
| `revert`   | Revert previous change                     | `revert: revert AKS deployment change`  |

The exact allowed types can be customized by an organization.

---

# 3. `feat` — New functionality

Use `feat` when you introduce a **new capability**.

Example:

```bash
git commit -m "feat: add Azure Container Registry deployment"
```

Another example:

```text
feat: add autoscaling configuration for AKS
```

This means:

> Something new can now be done that couldn't be done before.

### DevOps examples

```text
feat: add Terraform module for Azure Storage

feat: add Kubernetes ingress configuration

feat: add production deployment stage

feat: add Docker image vulnerability scanning

feat: add GitHub Actions workflow
```

---

# 4. `fix` — Bug correction

Use `fix` when you're correcting an existing problem.

```text
fix: correct Terraform state locking configuration
```

or:

```text
fix: resolve Kubernetes readiness probe failure
```

The distinction is:

```text
New capability → feat
Existing behavior is broken → fix
```

Example:

Before:

```text
Deployment fails because wrong resource group is used.
```

Commit:

```text
fix: use correct resource group in deployment pipeline
```

---

# 5. `docs` — Documentation

Use `docs` when the change only affects documentation.

Examples:

```text
docs: add Terraform installation instructions

docs: update AKS troubleshooting guide

docs: document production deployment process

docs: add Git branching strategy
```

If you change actual code **and** documentation:

```text
feat: add automated AKS deployment
```

rather than:

```text
docs: add AKS deployment
```

because the primary change is functionality.

---

# 6. `refactor` — Restructuring without changing behavior

This is one of the most important distinctions for senior engineers.

Suppose your Terraform works correctly:

```text
module A
module B
module C
```

You reorganize it into:

```text
modules/
  network/
  compute/
  security/
```

but the infrastructure behavior remains the same.

Use:

```text
refactor: reorganize Terraform modules
```

Another example:

```text
refactor: simplify Azure pipeline templates
```

### Mental model

```text
Behavior changes?
       │
   ┌───┴───┐
  YES      NO
   │        │
 feat/fix  refactor
```

---

# 7. `test` — Testing changes

Use:

```text
test: add unit tests for Terraform validation

test: add Kubernetes deployment tests

test: cover Docker image tagging logic
```

This means the primary purpose of the commit is testing.

---

# 8. `chore` — Maintenance

`chore` is commonly used for routine maintenance that doesn't represent a product feature or bug fix.

Examples:

```text
chore: update .gitignore

chore: remove obsolete scripts

chore: update development dependencies

chore: clean up repository configuration
```

Think:

> Maintenance work that doesn't directly represent application functionality.

---

# 9. `ci` — CI/CD changes

This one is especially important for **DevOps engineers**.

Use `ci` when modifying CI/CD configuration.

Examples:

```text
ci: add Terraform validation stage

ci: add SonarQube quality gate

ci: add Docker image scanning

ci: update GitHub Actions runner

ci: add deployment approval stage
```

For example:

```text
.github/workflows/deploy.yml
```

changed only to add:

```text
security scan
```

Commit:

```text
ci: add security scanning to deployment workflow
```

---

# 10. `build` — Build system

Use `build` when modifying the build system or build dependencies.

Examples:

```text
build: update Node.js build version

build: upgrade Docker base image

build: update Maven dependency configuration

build: optimize Docker build layers
```

### `build` vs `ci`

This distinction can sometimes be organization-specific.

A useful rule:

```text
How software itself is built
        ↓
build

How automation builds/tests/deploys it
        ↓
ci
```

Example:

```text
Dockerfile changed
→ build

.github/workflows/build.yml changed
→ ci
```

---

# 11. `perf` — Performance

Use `perf` when the primary purpose is improving performance.

Example:

```text
perf: reduce Docker image build time

perf: optimize Terraform deployment workflow

perf: improve pipeline caching
```

---

# 12. `style` — Formatting only

Use `style` for changes that don't affect behavior.

Examples:

```text
style: format YAML files

style: fix Terraform formatting

style: normalize shell script indentation
```

For example:

```bash
terraform fmt
```

followed by a commit:

```text
style: format Terraform configuration
```

---

# 13. `revert` — Reverting a previous commit

Git can create a revert commit:

```bash
git revert abc123
```

The resulting commit could be:

```text
revert: revert "feat: add production deployment"
```

This is particularly useful for shared branches because you're **adding a new commit that reverses the previous change** rather than rewriting shared history.

---

# 14. What is `scope`?

You can make commits more specific:

```text
feat(terraform): add Azure network module

fix(kubernetes): correct readiness probe

ci(github-actions): add security scanning

docs(terraform): update module documentation
```

Here:

```text
feat       → type
terraform  → scope
```

Format:

```text
type(scope): description
```

---

# 15. Why scope is useful for a senior DevOps team

Imagine a large repository:

```text
repo/
├── terraform/
├── kubernetes/
├── docker/
├── pipelines/
├── scripts/
└── documentation/
```

Instead of:

```text
fix: fix configuration
```

you can write:

```text
fix(terraform): correct backend configuration
```

or:

```text
ci(azure-pipelines): fix deployment variable
```

This makes history much easier to search.

---

# 16. Good vs bad commit messages

### ❌ Bad

```text
update

changes

fix

final changes

latest

working

code changes

done

minor changes
```

These tell future engineers almost nothing.

### ✅ Better

```text
fix(terraform): correct Azure backend configuration
```

```text
ci(azure-pipelines): add Terraform plan validation
```

```text
docs(kubernetes): document pod troubleshooting
```

---

# 17. Commit subject should describe the change

Bad:

```text
feat: changes
```

Good:

```text
feat: add automated AKS deployment
```

Better:

```text
feat(aks): add automated production deployment workflow
```

The commit should answer:

> **What changed?**

The body can answer:

> **Why did it change?**

---

# 18. Commit body

For important changes, you can use:

```text
feat(aks): add automated production deployment

Add a deployment workflow for AKS that:
- validates the Kubernetes manifests
- builds the container image
- pushes the image to ACR
- deploys to the production namespace
```

Conceptually:

```text
Header
  ↓
What changed?

Body
  ↓
Why/how/context?
```

---

# 19. Breaking changes

This is very important for senior interviews.

Suppose an API change breaks existing consumers.

You can write:

```text
feat(api)!: remove deprecated authentication endpoint
```

The `!` indicates a breaking change.

You can also use a footer:

```text
feat(api): change authentication mechanism

BREAKING CHANGE: clients must now use OAuth2 instead of the legacy token endpoint.
```

---

# 20. Why does this matter for DevOps?

Because Conventional Commits can be consumed by automation.

Imagine:

```text
feat:
fix:
docs:
chore:
```

CI/CD tooling can analyze these commits.

For example:

```text
feat
 ↓
minor release

fix
 ↓
patch release

BREAKING CHANGE
 ↓
major release
```

This is commonly associated with **Semantic Versioning**.

Conceptually:

```text
MAJOR.MINOR.PATCH
   │      │     │
   │      │     └── fix
   │      └──────── feat
   └─────────────── breaking change
```

The exact release automation depends on the team's tooling and configuration.

---

# 21. Example DevOps repository history

A healthy repository might look like:

```text
feat(aks): add production deployment

fix(terraform): correct subnet association

ci(github-actions): add security scanning

docs(terraform): document state management

refactor(terraform): split network module

test(terraform): add module validation

chore(deps): update Terraform provider version

perf(docker): optimize image build cache
```

When someone looks at the history six months later, they can understand what happened.

---

# 22. Senior-level example

Suppose you changed:

```text
Azure DevOps pipeline
Terraform
Docker
Kubernetes
```

Don't create one vague commit:

```text
changes for deployment
```

Depending on the actual logical changes, you might have:

```text
ci(azure-pipelines): add Terraform plan validation

build(docker): optimize production image layers

feat(aks): add Kubernetes deployment stage

docs(deployment): document production release process
```

This makes each change independently understandable.

---

# 23. Should every tiny change have a separate commit?

No.

Don't turn your history into:

```text
fix typo

fix typo again

fix typo again

change variable

change variable

final fix

final final fix
```

During development, you can make many commits.

Before merging, you can clean the branch using:

```bash
git rebase -i
```

or use squash merging depending on the team's workflow.

The goal is **meaningful history**, not artificially small commits.

---

# 24. One commit = one logical change

This is one of the most useful senior practices.

Prefer:

```text
feat(terraform): add Azure storage module
```

instead of:

```text
feat: add storage + fix Kubernetes + update README + change pipeline
```

Why?

Because later you may need:

```bash
git revert <commit>
```

If one commit contains five unrelated changes, reverting it becomes dangerous.

---

# 25. DevOps example: Terraform

Suppose you create:

```text
terraform/modules/network
```

Commit:

```text
feat(terraform): add reusable Azure network module
```

Then fix a bug:

```text
fix(terraform): correct subnet address range
```

Then documentation:

```text
docs(terraform): document network module inputs
```

Then formatting:

```text
style(terraform): format module configuration
```

This creates clean history.

---

# 26. DevOps example: Kubernetes

```text
feat(kubernetes): add deployment manifest

feat(kubernetes): add horizontal pod autoscaler

fix(kubernetes): correct readiness probe path

fix(kubernetes): increase deployment resource limits

docs(kubernetes): document rollout procedure
```

---

# 27. DevOps example: Docker

```text
build(docker): upgrade Python base image

perf(docker): reduce production image size

fix(docker): correct container healthcheck

docs(docker): document local image build
```

---

# 28. DevOps example: GitHub Actions

```text
ci(actions): add Terraform validation workflow

ci(actions): add Docker image scanning

ci(actions): cache Terraform providers

fix(actions): correct Azure authentication configuration

chore(actions): upgrade checkout action
```

---

# 29. DevOps example: Azure DevOps

```text
ci(azure-pipelines): add Terraform plan stage

ci(azure-pipelines): add manual production approval

fix(azure-pipelines): correct service connection reference

refactor(azure-pipelines): reuse deployment template

docs(azure-pipelines): document release workflow
```

---

# 30. How to enforce Conventional Commits

A mature organization shouldn't depend only on developers remembering the format.

You can enforce it.

```text
Developer
    |
    ▼
git commit
    |
    ▼
commit-msg hook
    |
    ├── valid → continue
    │
    └── invalid → reject
```

Then enforce again centrally:

```text
Developer
    ↓
Pull Request
    ↓
CI validation
    ↓
Commit/PR policy
    ↓
Merge
```

This is important because local hooks can be bypassed.

---

# 31. Example commit validation

A policy might allow:

```text
^(feat|fix|docs|refactor|test|chore|ci|build|perf|style)
```

So:

```text
feat: add deployment
```

passes.

But:

```text
update stuff
```

fails.

In a real organization, the exact allowed types and validation rules should be defined centrally.

---

# 32. Commit vs Pull Request title

Don't confuse them.

Commit:

```text
feat(aks): add production deployment
```

PR:

```text
Add automated AKS production deployment
```

A PR can contain multiple commits:

```text
PR
│
├── feat(aks): add deployment
├── test(aks): add deployment validation
├── fix(aks): correct namespace
└── docs(aks): update deployment guide
```

The final merge strategy may preserve, squash, or otherwise combine those commits.

---

# 33. How a senior engineer should think about commit messages

Think about the engineer who will investigate a production issue **two years from now**.

Suppose they run:

```bash
git log --oneline
```

and see:

```text
a123456 fix(kubernetes): correct readiness probe
b234567 ci(actions): add deployment validation
c345678 feat(aks): add production deployment
d456789 refactor(terraform): split network module
```

They immediately get context.

Compare:

```text
a123456 fix
b234567 changes
c345678 update
d456789 final
```

The second history creates unnecessary investigation effort.

---

# 34. The most important types to memorize

For your **15-year DevOps interview**, I would memorize these first:

```text
feat
fix
docs
refactor
test
chore
ci
build
perf
revert
```

Then understand:

```text
scope
!
BREAKING CHANGE
```

---

# 35. Interview question: Is Conventional Commits mandatory in Git?

### Answer

No.

Git itself does not require:

```text
feat:
fix:
docs:
```

These are conventions.

They become valuable when organizations use them for:

* Consistent history
* Automated changelogs
* Semantic versioning
* Release automation
* Commit validation
* CI/CD rules

So the senior answer is:

> "Conventional Commits is a convention, not a Git requirement. I use it when the organization benefits from standardized commit history and automation."

---

# 36. Interview question: `fix` vs `refactor`?

### Answer

Use `fix` when behavior is incorrect and I'm correcting it.

Use `refactor` when I'm restructuring the implementation without intentionally changing externally observable behavior.

Example:

```text
fix(terraform): correct invalid subnet configuration
```

versus:

```text
refactor(terraform): split network configuration into modules
```

---

# 37. Interview question: `feat` vs `chore`?

### Answer

`feat` introduces user/business functionality.

`chore` represents maintenance that doesn't introduce that functionality.

Example:

```text
feat: add automated AKS deployment
```

versus:

```text
chore: update repository configuration
```

---

# 38. Interview question: `ci` vs `build`?

### Answer

A practical distinction is:

```text
Build system/tooling
       ↓
build
```

and:

```text
CI/CD automation
       ↓
ci
```

Examples:

```text
build: upgrade Docker base image
```

```text
ci: add Docker build stage to GitHub Actions
```

Organizations can define their own convention if necessary.

---

# 39. Interview question: Why are meaningful commits important for DevOps?

### Answer

Because Git history is part of the engineering audit trail.

Meaningful commits help with:

* Incident investigation
* `git bisect`
* Revert operations
* Release notes
* Change tracking
* Code review
* Compliance
* Root-cause analysis

For example:

```text
Production issue
      ↓
Identify bad release
      ↓
Find relevant commit
      ↓
Understand change
      ↓
Revert/fix
```

Good commit messages reduce the time required for this investigation.

---

# 40. My recommended standard for a senior DevOps team

A practical convention could be:

```text
<type>(<scope>): <imperative description>
```

Examples:

```text
feat(aks): add blue-green deployment

fix(terraform): correct remote state configuration

ci(actions): add Terraform security scanning

build(docker): upgrade Alpine base image

refactor(terraform): extract reusable network module

test(kubernetes): add deployment smoke tests

docs(helm): document values configuration

chore(deps): update Terraform provider

perf(docker): optimize image build cache

revert(aks): revert production deployment change
```

### For breaking changes

```text
feat(api)!: remove legacy authentication endpoint
```

or:

```text
feat(api): replace legacy authentication

BREAKING CHANGE: clients must migrate to OAuth2.
```

---

# Final Mental Model

Remember this:

```text
feat
 ↓
NEW functionality

fix
 ↓
BUG correction

docs
 ↓
DOCUMENTATION

refactor
 ↓
CODE STRUCTURE without intended behavior change

test
 ↓
TESTING

chore
 ↓
MAINTENANCE

ci
 ↓
CI/CD automation

build
 ↓
BUILD system/dependencies

perf
 ↓
PERFORMANCE

style
 ↓
FORMATTING

revert
 ↓
UNDO previous change
```

And the senior-level principle is:

> **A good commit message should make the change understandable without opening the code, while the commit itself should represent one logical change.**

For a 15-year DevOps engineer, don't stop at memorizing `feat`, `fix`, and `docs`. Understand how **Conventional Commits + PRs + branch protection + CI/CD + semantic versioning + automated releases** form one complete engineering workflow.
:::
