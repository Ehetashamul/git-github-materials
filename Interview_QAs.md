# Top 50 Git & GitHub Interview Questions and Answers

## DevOps Engineer — 15 Years Experience

---

# SECTION 1 — GIT FUNDAMENTALS

## 1. What is Git?

### Answer

Git is a **distributed version control system (DVCS)** used to track changes in source code and other files.

Unlike centralized version control systems, every Git clone contains its own repository history, allowing developers to work and commit locally without continuous access to a central server.

### Key characteristics

* Distributed
* Fast local operations
* Branching and merging
* Complete history available locally
* Supports offline commits
* Cryptographic object identification
* Supports multiple workflows

### Senior-level point

Git is the version-control engine. GitHub, GitLab, and Azure Repos are platforms built around Git that provide collaboration, hosting, access control, pull requests, CI/CD integration, and other capabilities.

---

# 2. What is the difference between Git and GitHub?

### Answer

**Git** is the version-control system.

**GitHub** is a cloud-based collaboration and repository-hosting platform built around Git.

| Git                  | GitHub                       |
| -------------------- | ---------------------------- |
| Version-control tool | Collaboration platform       |
| Runs locally         | Primarily server/cloud based |
| Tracks history       | Hosts repositories           |
| Branching/merging    | Pull Requests                |
| Local commits        | Code review                  |
| CLI                  | Web UI + APIs                |
| Local repository     | Remote repository            |

Example:

```bash
git commit
git branch
git merge
```

are Git operations.

A GitHub Pull Request, branch protection rule, Actions workflow, or CODEOWNERS configuration is a GitHub platform capability.

---

# 3. Explain the Git working model.

### Answer

A typical Git workflow consists of:

```text
Working Directory
       |
       | git add
       v
Staging Area
       |
       | git commit
       v
Local Repository
       |
       | git push
       v
Remote Repository
```

### Commands

```bash
git status

git add file.txt

git commit -m "Add configuration"

git push origin main
```

The staging area allows us to construct the exact set of changes that should become the next commit.

### Senior point

Git does not directly commit everything in the working directory. The staging index acts as a boundary between working changes and the next commit.

---

# 4. What is a Git repository?

### Answer

A Git repository is the database that stores the project's version history, objects, references, and configuration.

A normal working repository contains:

```text
project/
├── source files
├── .git/
└── configuration
```

The `.git` directory contains Git's internal data.

Important components include:

```text
.git/
├── objects/
├── refs/
├── HEAD
├── index
├── config
└── logs/
```

---

# 5. What is a Git commit?

### Answer

A commit is a snapshot of the project state together with metadata describing the change.

A commit contains information such as:

* Author
* Committer
* Parent commit(s)
* Commit message
* Timestamp
* Tree reference

Conceptually:

```text
Commit
 ├── parent
 ├── author
 ├── timestamp
 ├── message
 └── tree
       ├── files
       └── directories
```

Git identifies objects using cryptographic hashes.

---

# 6. What is HEAD in Git?

### Answer

`HEAD` represents the currently checked-out position in the repository.

Normally:

```text
HEAD
 |
 v
main
 |
 v
C3
```

So:

```bash
git branch
```

may show:

```text
* main
```

`HEAD` normally points to the current branch, and the branch points to a commit.

### Detached HEAD

If you checkout a commit directly:

```bash
git checkout <commit>
```

you can get:

```text
HEAD
 |
 v
C3
```

instead of:

```text
HEAD
 |
 v
main
 |
 v
C3
```

This is called detached HEAD state.

---

# 7. What is a Git branch?

### Answer

A Git branch is essentially a **movable reference to a commit**.

Example:

```text
A---B---C
        ^
        |
       main
```

After a new commit:

```text
A---B---C---D
            ^
            |
           main
```

The branch moves automatically as new commits are created.

### Senior point

A branch does not contain a separate copy of the repository.

It is a lightweight reference pointing to a commit.

---

# 8. What is the difference between a branch and a commit?

### Answer

A commit is an immutable historical object representing a project state.

A branch is a movable reference pointing to a commit.

```text
A---B---C
        ^
        |
       main
```

Here:

```text
C = commit
main = branch reference
```

After another commit:

```text
A---B---C---D
            ^
            |
           main
```

The commit remains in history while the branch reference moves.

---

# 9. What is `git clone`?

### Answer

`git clone` creates a local repository from a remote repository.

Example:

```bash
git clone https://github.com/company/project.git
```

It typically creates:

```text
working directory
+
local Git repository
+
remote-tracking references
```

The default remote is normally named:

```text
origin
```

---

# 10. What is `origin`?

### Answer

`origin` is simply the default name Git usually assigns to the remote repository when you clone a repository.

Example:

```bash
git remote -v
```

may show:

```text
origin  https://github.com/company/project.git
```

`origin` is not a special Git server.

You can have:

```text
origin
upstream
backup
company
```

as remote names.

---

# SECTION 2 — BRANCHING AND MERGING

# 11. Explain Git branching strategies.

### Answer

Common strategies include:

* Feature branching
* Git Flow
* GitHub Flow
* GitLab Flow
* Trunk-Based Development
* Release branching
* Environment branching

For modern CI/CD systems, a common pattern is:

```text
feature/*
     |
     v
Pull Request
     |
     v
protected main
     |
     v
CI/CD
     |
     v
environments
```

The choice depends on:

* Deployment frequency
* Release model
* QA process
* Number of supported versions
* CI/CD maturity
* Regulatory requirements

### Senior point

I don't choose a branching strategy simply because it is popular. I choose it based on the organization's release and deployment model.

---

# 12. Merge vs Rebase?

### Answer

Both integrate changes, but they do it differently.

### Merge

```text
A---B---C---M
     \     /
      D---E
```

Merge preserves the existing branch history and creates a merge commit when necessary.

### Rebase

```text
A---B---C---D'---E'
```

Rebase moves/replays commits on top of another base.

### Key difference

Merge:

> Preserves history topology.

Rebase:

> Rewrites commit ancestry to create a different history.

### Senior rule

I generally avoid rebasing shared/public branches because it rewrites commit identities.

---

# 13. What is a fast-forward merge?

### Answer

A fast-forward occurs when the target branch has no divergent commits.

Before:

```text
A---B
     \
      C
```

If `main` is at B and feature is at C:

```text
main → B
feature → C
```

Git can simply move `main` to C:

```text
A---B---C
        ^
        |
       main
```

No merge commit is required.

Command:

```bash
git merge feature
```

---

# 14. What is a merge conflict?

### Answer

A merge conflict occurs when Git cannot automatically reconcile changes.

Example:

```text
main:
A---B---C

feature:
     \
      D
```

Suppose both C and D modify the same lines.

Git may report:

```text
CONFLICT
```

The developer must resolve the conflict manually.

For a merge:

```bash
git status

# edit files

git add <file>

git commit
```

### Senior approach

I don't simply resolve conflicts mechanically. I first determine:

* Why both branches changed the same code
* Whether one change supersedes another
* Whether the resulting code remains functionally correct
* Whether regression tests are required

---

# 15. Merge conflict vs rebase conflict?

### Answer

The underlying problem is similar, but the workflow differs.

### Merge

```bash
git merge feature
```

Resolve conflict:

```bash
git add .
git commit
```

### Rebase

```bash
git rebase main
```

Resolve conflict:

```bash
git add .
git rebase --continue
```

To abandon the rebase:

```bash
git rebase --abort
```

### Senior point

A rebase may produce multiple conflicts because Git is replaying commits one by one.

---

# 16. Fetch vs Pull?

### Answer

`git fetch` downloads remote updates without integrating them into the current branch.

```bash
git fetch origin
```

It updates references such as:

```text
origin/main
```

but does not modify your current branch.

`git pull` normally performs:

```text
git fetch
+
integration
```

The integration may be a merge or rebase depending on configuration/options.

### Senior preference

For controlled workflows, I often prefer:

```bash
git fetch
git diff
git log
```

and then explicitly choose merge/rebase.

This avoids blindly integrating remote changes.

---

# 17. Local branch vs remote-tracking branch?

### Answer

Local branch:

```text
main
```

is a branch you normally work on.

Remote-tracking branch:

```text
origin/main
```

is your local record of the state of the remote branch as last observed through fetch.

Example:

```text
Remote:
main → C5

Local:
main → C4
origin/main → C5
```

This tells us:

```text
local main is behind origin/main
```

---

# 18. What is `git pull --rebase`?

### Answer

Instead of creating a merge commit after fetching, Git rebases local commits on top of the updated remote branch.

Suppose:

```text
origin/main:
A---B---C

local:
A---B---D
```

After:

```bash
git pull --rebase
```

the local history becomes approximately:

```text
A---B---C---D'
```

This can keep history linear.

### Important

The rebased commit `D'` has a different identity from `D`.

---

# 19. Cherry-pick vs merge?

### Answer

Merge integrates the history of another branch.

Cherry-pick applies the changes introduced by a specific commit onto the current branch.

Example:

```bash
git cherry-pick abc123
```

Suppose:

```text
main:
A---B---C

feature:
     \
      D
```

Cherry-pick D onto main creates a new commit:

```text
A---B---C---D'
```

### Typical use

* Hotfix
* Backporting
* Selective release changes

### Risk

Repeated cherry-picking can create duplicated logical changes and make history harder to understand.

---

# 20. What is the difference between reset and revert?

### Answer

`reset` moves Git references and can change local history.

`revert` creates a new commit that reverses an earlier commit.

### Reset

```text
A---B---C
        ^
       main
```

After:

```bash
git reset --hard B
```

you get:

```text
A---B
    ^
   main
```

### Revert

```text
A---B---C---D
```

where D reverses C.

History remains intact:

```text
A---B---C---D
```

### Senior production rule

For already-published shared history, I normally prefer `revert`.

Reset is more appropriate for controlled local history manipulation.

---

# SECTION 3 — GIT RESET, RECOVERY AND HISTORY

# 21. Explain soft, mixed and hard reset.

### Answer

Suppose:

```text
HEAD → C
```

and we execute:

```bash
git reset <mode> B
```

### Soft

```bash
git reset --soft B
```

Moves HEAD but preserves:

* Working tree
* Staging area

### Mixed

```bash
git reset --mixed B
```

Moves HEAD and resets staging, but preserves working files.

This is the default.

### Hard

```bash
git reset --hard B
```

Moves HEAD and resets:

* Staging area
* Working tree

So local uncommitted changes can be lost.

| Reset | HEAD  | Staging   | Working Tree |
| ----- | ----- | --------- | ------------ |
| soft  | moved | preserved | preserved    |
| mixed | moved | reset     | preserved    |
| hard  | moved | reset     | reset        |

---

# 22. What is reflog?

### Answer

Reflog records movements of references such as `HEAD` and local branches.

Example:

```bash
git reflog
```

You may see:

```text
abc123 HEAD@{0}: reset: moving to HEAD~2
def456 HEAD@{1}: commit: Add payment
```

This is extremely useful when someone accidentally runs:

```bash
git reset --hard
```

and needs to recover previous work.

### Senior point

`git log` shows commit history.

`git reflog` shows the history of reference movement.

---

# 23. How do you recover a deleted commit?

### Answer

If the commit was recently reachable, first inspect reflog:

```bash
git reflog
```

Find the lost commit:

```text
abc123
```

Then create a recovery branch:

```bash
git switch -c recovery abc123
```

This protects the recovered commit from becoming unreachable.

### Important

Recovery becomes more difficult after unreachable objects are garbage-collected.

---

# 24. What is `git fsck`?

### Answer

`git fsck` checks the integrity of the Git object database.

It can help identify:

* Dangling commits
* Unreachable objects
* Corrupt objects

Example:

```bash
git fsck --full
```

It is useful during advanced repository recovery and troubleshooting.

---

# 25. What is Git garbage collection?

### Answer

Git periodically cleans up unreachable objects and optimizes repository storage.

Command:

```bash
git gc
```

Git may:

* Compress objects
* Repack objects
* Remove unreachable objects after appropriate expiration
* Improve repository performance

### Senior caution

If recovering accidentally deleted work, don't immediately perform aggressive cleanup. First investigate reflog and unreachable objects.

---

# SECTION 4 — ADVANCED GIT

# 26. Explain Git's object model.

### Answer

Git primarily stores objects such as:

```text
Blob
Tree
Commit
Tag
```

### Blob

Stores file content.

### Tree

Represents directories and maps names to blobs/trees.

### Commit

Points to a tree and parent commit(s), along with metadata.

### Tag

Can reference another Git object, commonly a commit.

Conceptually:

```text
Commit
  |
  v
 Tree
 ├── Blob
 ├── Blob
 └── Tree
       └── Blob
```

This object model is fundamental to understanding Git internals.

---

# 27. What is `git merge-base`?

### Answer

`git merge-base` finds a common ancestor between branches.

Example:

```bash
git merge-base main feature
```

This identifies the best common ancestor Git can use as a base for a three-way merge.

Conceptually:

```text
        C
       / \
A---B     D---E
       \ /
        ?
```

The merge base helps determine:

```text
what changed on branch A
vs
what changed on branch B
```

---

# 28. What is a three-way merge?

### Answer

Git generally compares three versions:

```text
             merge base
                |
        ┌───────┴───────┐
        ▼               ▼
      branch A        branch B
```

For example:

```text
        B
       / \
      C   D
```

Git compares:

```text
B → C
B → D
```

and attempts to combine both sets of changes.

This is more reliable than simply comparing the two final files.

---

# 29. What is interactive rebase?

### Answer

Interactive rebase allows you to modify a sequence of commits.

Example:

```bash
git rebase -i HEAD~5
```

You can:

```text
pick
reword
edit
squash
fixup
drop
```

Typical use:

```text
Before:

A
B
"fix typo"
"fix previous"
"debug"
C
```

After cleanup:

```text
A
B
C
```

### Senior point

Interactive rebase is useful for cleaning private branch history before review.

It should be used carefully on shared branches because it rewrites commit identities.

---

# 30. What is `git rebase --onto`?

### Answer

`rebase --onto` allows you to move a range of commits from one base to another.

Example:

```bash
git rebase --onto main old-base feature
```

Conceptually:

```text
old-base---A---B---C
     \
      D---E
```

can be transformed so that:

```text
main---D'---E'
```

This is particularly useful for advanced branch restructuring.

---

# 31. What is `git push --force-with-lease`?

### Answer

It force-updates a remote branch but performs an additional safety check.

```bash
git push --force-with-lease
```

Compared with:

```bash
git push --force
```

`--force-with-lease` attempts to ensure that the remote branch hasn't changed unexpectedly since you last observed it.

### Senior recommendation

Prefer:

```bash
git push --force-with-lease
```

over:

```bash
git push --force
```

when history rewriting is genuinely required.

Even then, avoid rewriting shared protected branches.

---

# 32. What is `git bisect`?

### Answer

`git bisect` performs a binary search through commit history to identify which commit introduced a bug.

Start:

```bash
git bisect start
```

Mark current commit as bad:

```bash
git bisect bad
```

Mark a known good commit:

```bash
git bisect good <commit>
```

Git checks out a midpoint.

You test it and tell Git:

```bash
git bisect good
```

or:

```bash
git bisect bad
```

Eventually Git identifies the likely offending commit.

### Senior use

Very useful for regression investigation in large repositories.

---

# 33. What are `git bisect run` and automated bisect?

### Answer

If a deterministic test can identify whether a commit is good or bad, bisect can be automated.

Example:

```bash
git bisect run ./test.sh
```

Git repeatedly checks out commits and executes the test.

This can dramatically reduce manual debugging effort.

---

# 34. What is `git rerere`?

### Answer

`rerere` means:

> reuse recorded resolution.

Git can remember how you resolved a previous merge conflict.

Enable:

```bash
git config --global rerere.enabled true
```

If the same conflict appears again, Git may reuse the previous resolution.

This can be valuable in workflows involving:

* long-lived branches
* repeated rebases
* release branches
* backports

---

# 35. What are Git hooks?

### Answer

Git hooks are scripts executed at specific Git lifecycle points.

Examples:

```text
pre-commit
commit-msg
pre-push
post-merge
```

Example:

```text
git commit
    |
    v
pre-commit
    |
    v
commit-msg
    |
    v
commit created
```

Hooks can enforce local checks such as:

* Formatting
* Linting
* Commit message validation
* Secret detection

### Senior point

Hooks are not a substitute for CI/CD because local hooks can be bypassed or may not exist on every developer machine.

Critical controls should be enforced centrally.

---

# SECTION 5 — GITHUB

# 36. What is a Pull Request?

### Answer

A Pull Request is a collaboration mechanism for proposing changes from one branch into another.

Typical workflow:

```text
feature
   |
   v
Pull Request
   |
   ├── Review
   ├── CI
   ├── Security checks
   └── Quality checks
   |
   v
main
```

A PR provides:

* Code review
* Discussion
* Automated validation
* Approval workflow
* Traceability
* Integration controls

---

# 37. What is GitHub Actions?

### Answer

GitHub Actions is GitHub's automation and CI/CD platform.

A workflow is typically defined in:

```text
.github/workflows/
```

Example:

```yaml
name: CI

on:
  pull_request:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - run: npm install

      - run: npm test
```

It can automate:

* Build
* Test
* Security scanning
* Docker builds
* Infrastructure deployment
* Releases

---

# 38. GitHub Actions vs GitHub repository?

### Answer

The repository stores source code and Git history.

GitHub Actions provides automation.

```text
Repository
   |
   ├── Source code
   ├── Branches
   ├── PRs
   └── Configuration
          |
          v
     GitHub Actions
          |
          ├── Build
          ├── Test
          ├── Scan
          └── Deploy
```

They are related but separate capabilities.

---

# 39. What is branch protection?

### Answer

Branch protection prevents unsafe changes to important branches.

Example rules for `main`:

```text
Direct push              ❌
PR required              ✅
CI required               ✅
Code review               ✅
Status checks             ✅
Force push                ❌
Branch deletion           ❌
```

This turns repository governance into enforceable controls.

---

# 40. What is CODEOWNERS?

### Answer

`CODEOWNERS` defines reviewers responsible for specific parts of a repository.

Example:

```text
/infrastructure/ @platform-team
/security/        @security-team
/docs/            @documentation-team
```

When a PR changes those paths, the appropriate owners can be requested or required for review depending on repository rules.

### Senior use

CODEOWNERS can help implement ownership boundaries in large repositories.

---

# SECTION 6 — GIT/GITHUB SECURITY

# 41. What happens if someone commits a secret to Git?

### Answer

Deleting the file in a later commit does **not** remove the secret from Git history.

Example:

```text
Commit 1 → secret committed
Commit 2 → file deleted
```

The secret may still exist in:

```text
Commit 1
```

### Correct response

1. Revoke/rotate the credential immediately.
2. Determine exposure.
3. Remove the secret from repository history if appropriate.
4. Verify the rewritten history.
5. Force-update affected branches carefully.
6. Notify relevant security stakeholders.

### Most important point

> **Removing a secret from Git is not the same as invalidating the secret.**

Rotation comes first.

---

# 42. How do you prevent secrets from being committed?

### Answer

Use multiple layers:

```text
Developer
   |
   ├── pre-commit secret scanning
   |
   ▼
Pull Request
   |
   ├── secret scanning
   ├── SAST
   └── dependency scanning
   |
   ▼
CI/CD
```

And use:

* Secret managers
* Environment variables
* GitHub encrypted secrets
* Cloud identity mechanisms
* Short-lived credentials
* OIDC where supported

Avoid storing:

```text
password
API key
private key
cloud secret
```

directly in source control.

---

# 43. What is GitHub OIDC and why is it useful in DevOps?

### Answer

OIDC allows a CI/CD workload to obtain short-lived cloud credentials based on an identity token instead of storing long-lived cloud credentials in repository secrets.

Conceptually:

```text
GitHub Actions
      |
      | OIDC token
      v
Cloud Identity Provider
      |
      | temporary credentials
      v
Azure/AWS/GCP
```

This reduces dependence on long-lived static credentials.

### Senior security principle

Prefer:

```text
short-lived identity
```

over:

```text
long-lived secret
```

whenever practical.

---

# SECTION 7 — REPOSITORY AND PERFORMANCE

# 44. What is a shallow clone?

### Answer

A shallow clone downloads limited commit history.

Example:

```bash
git clone --depth 1 <repo>
```

Instead of downloading the entire history, Git retrieves only a limited depth.

### Benefits

* Faster clone
* Lower bandwidth
* Lower storage
* Useful for CI

### Drawbacks

Some history-dependent operations may not work correctly without additional history.

For example:

```text
git log
git bisect
merge-base
version calculations
```

may require additional history.

---

# 45. What is Git LFS?

### Answer

Git LFS means Git Large File Storage.

It is designed for large binary files such as:

* Videos
* Large datasets
* Design assets
* Large binaries

Instead of storing the large file directly in normal Git object storage, Git tracks a pointer while the actual content is stored through LFS infrastructure.

Conceptually:

```text
Git repository
     |
     └── pointer file
             |
             v
          Git LFS
             |
             v
        Large object
```

This prevents large binary files from unnecessarily bloating normal Git history.

---

# 46. What is sparse checkout?

### Answer

Sparse checkout allows a repository to be checked out with only selected directories/files.

Useful for large monorepositories.

Conceptually:

```text
Repository

/apps
/services
/infrastructure
/docs
/tools
```

Developer may need only:

```text
/services/payment
```

Sparse checkout avoids populating unnecessary parts of the working tree.

---

# 47. What is a monorepo?

### Answer

A monorepo stores multiple applications/services/components in one repository.

Example:

```text
repo/
├── services/
│   ├── payment/
│   ├── user/
│   └── notification/
├── frontend/
├── infrastructure/
└── shared/
```

### Advantages

* Centralized versioning
* Shared code
* Easier cross-component changes
* Unified tooling

### Challenges

* Repository size
* CI scalability
* Dependency management
* Ownership
* Build optimization

Advanced techniques include:

* Sparse checkout
* Partial clone
* Path-based CI
* Build caching
* CODEOWNERS

---

# 48. How would you handle a huge Git repository?

### Answer

I would first identify the actual problem.

Possible issues:

```text
Large history
Large binary files
Too many objects
Large working tree
Slow CI
Large monorepo
```

Then apply appropriate techniques:

```text
Git LFS
Sparse checkout
Partial clone
Shallow clone
Repository cleanup
History rewriting
Build caching
Path-based CI
Monorepo tooling
```

I would avoid blindly deleting history.

First I would analyze:

```bash
git count-objects -vH
git rev-list --objects --all
```

and repository-specific statistics.

### Senior principle

> Optimize the actual bottleneck instead of applying Git commands blindly.

---

# SECTION 8 — SENIOR DEVOPS SCENARIOS

# 49. Production is broken because a bad commit was merged into main. What do you do?

### Answer

First I determine whether the change should be reverted or whether there is a safer mitigation such as disabling a feature flag.

If the bad change is already shared and deployed, I generally use:

```bash
git revert <commit>
```

Then:

```text
revert commit
     |
     v
PR
     |
     v
CI
     |
     v
main
     |
     v
deployment
```

I would also:

1. Assess production impact.
2. Stop further rollout if necessary.
3. Roll back or redeploy the last known-good artifact.
4. Validate service health.
5. Investigate root cause.
6. Add regression tests.
7. Re-enable the feature/change only after validation.

### Senior point

Git history manipulation is not the first priority during a production incident.

**Restore service first, then clean up safely.**

---

# 50. A developer accidentally force-pushed and removed important commits. How do you recover?

### Answer

I would avoid making further destructive changes immediately.

First:

```bash
git fetch origin
```

Then investigate local reflogs and other clones.

Possible recovery sources:

```text
developer reflog
CI workspace
another developer's clone
remote-tracking references
tags
backup
Git hosting recovery mechanisms
```

Use:

```bash
git reflog
```

to locate the lost commit.

If found:

```bash
git switch -c recovery <commit>
```

Then validate the recovered history.

After that, restore the intended branch state carefully.

### Preventive controls

For important branches:

```text
Protected branch
       |
       ├── No force push
       ├── PR required
       ├── Required checks
       └── Required reviewers
```

For developers who genuinely need to rewrite private branches:

```bash
git push --force-with-lease
```

is safer than unconditional:

```bash
git push --force
```

---

# BONUS: 15 SENIOR FOLLOW-UP QUESTIONS

After these 50, interviewers often drill deeper with questions such as:

## 51. Why does rebase change commit SHA?

Because the commit's parent is part of the commit identity. When a commit is replayed onto a different parent, its resulting metadata/content relationship changes, producing a new commit object.

---

## 52. Why is a Git commit immutable?

A commit is identified by its content and metadata, including its parent relationship. Changing the commit effectively creates a different commit object.

---

## 53. Why does changing an old commit affect all descendant SHAs?

Because each descendant commit references its parent. Changing an ancestor changes the parent relationship and therefore changes the identity of descendant commits.

---

## 54. How does Git detect file changes?

Git primarily tracks content rather than storing a simple database of "line changed" records. It compares snapshots/object content and uses mechanisms such as hashes and diff algorithms to determine changes.

---

## 55. Why is Git merge usually safer than manual file copying?

Git understands repository ancestry and can use merge-base information and three-way merging to integrate changes while detecting conflicts.

---

## 56. What is a dangling commit?

A commit object that is no longer reachable through the normal references but still exists in the Git object database.

It may sometimes be recoverable through:

```bash
git fsck
```

or reflog.

---

## 57. What is a remote-tracking branch?

A local reference representing the last known state of a branch on a remote repository.

Example:

```text
origin/main
```

It is updated by commands such as:

```bash
git fetch
```

---

## 58. Why should CI generally build an immutable artifact?

Because rebuilding the same source later can potentially produce a different result due to dependency, compiler, base-image, or external-environment changes.

Better:

```text
Source
  ↓
Build once
  ↓
Artifact v1.2.3
  ↓
DEV
  ↓
QA
  ↓
PROD
```

---

## 59. What is the difference between Git tag and branch?

A branch is a movable reference.

A tag generally identifies a specific point in history.

Example:

```text
A---B---C---D
        ^   ^
        |   |
      v1.0 main
```

`main` may move to D while `v1.0` continues identifying C.

---

## 60. How would you design Git governance for a 500-developer organization?

I would establish:

```text
Repository standards
       ↓
Branching strategy
       ↓
Protected branches
       ↓
CODEOWNERS
       ↓
PR requirements
       ↓
CI quality gates
       ↓
Security scanning
       ↓
Secret scanning
       ↓
Release/versioning policy
       ↓
Auditability
```

I would also define:

* Naming conventions
* Merge strategy
* Commit standards
* Repository ownership
* Access control
* Emergency change process
* Branch retention
* Release tagging
* Disaster recovery
* Credential management

The goal is to make the safe path the easiest path.

---

# QUICK SENIOR INTERVIEW REVISION TABLE

| Topic              | Senior-level takeaway                     |
| ------------------ | ----------------------------------------- |
| Git                | Distributed version control               |
| GitHub             | Collaboration/hosting platform around Git |
| Branch             | Movable reference                         |
| Commit             | Snapshot + metadata                       |
| HEAD               | Current checkout position                 |
| Fetch              | Download remote updates                   |
| Pull               | Fetch + integrate                         |
| Merge              | Preserve branch topology                  |
| Rebase             | Replay commits onto another base          |
| Reset              | Move local history/reference              |
| Revert             | Create inverse commit                     |
| Cherry-pick        | Apply selected commit                     |
| Reflog             | Reference movement history                |
| Log                | Commit history                            |
| Merge-base         | Common ancestor                           |
| Bisect             | Binary-search regression                  |
| Rerere             | Reuse conflict resolution                 |
| Hooks              | Local Git lifecycle automation            |
| PR                 | Review + integration mechanism            |
| Branch protection  | Central governance                        |
| CODEOWNERS         | Path-based ownership                      |
| Git LFS            | Large-file handling                       |
| Shallow clone      | Limited history                           |
| Sparse checkout    | Limited working tree                      |
| Monorepo           | Multiple components in one repo           |
| OIDC               | Short-lived CI/CD identity                |
| GitHub Actions     | CI/CD automation                          |
| Feature flags      | Decouple deployment from release          |
| Immutable artifact | Build once, promote same artifact         |

---

# THE 10 QUESTIONS I WOULD EXPECT AT 15 YEARS EXPERIENCE

If the interviewer wants to distinguish a senior engineer from someone who only knows Git commands, expect deeper discussion around:

1. **Why would you choose trunk-based development over Git Flow?**
2. **How do you design Git branching for continuous deployment?**
3. **How do you recover accidentally deleted commits?**
4. **What exactly happens internally during rebase?**
5. **Why does rebase change commit SHA?**
6. **How do you handle a leaked production secret in Git history?**
7. **How do you design Git governance for hundreds of developers?**
8. **How would you optimize a 20-GB monorepo?**
9. **How do you safely handle production hotfixes?**
10. **How do you integrate Git with CI/CD while maintaining auditability and security?**

---

# FINAL SENIOR MENTAL MODEL

A 15-year DevOps engineer should not think of Git as merely:

```text
git add
git commit
git push
```

Think of the complete system:

```text
                 SOURCE CONTROL
                       │
                       ▼
                 Git Repository
                       │
              ┌────────┴────────┐
              │                 │
        Branch Strategy      Governance
              │                 │
              ▼                 ▼
          Pull Request     CODEOWNERS
              │             Protection
              │             Security
              ▼
             CI
      ┌───────┼────────┐
      │       │        │
    Build   Test    Security
      │       │        │
      └───────┼────────┘
              ▼
          Artifact
              │
              ▼
             CD
              │
       ┌──────┼──────┐
       ▼      ▼      ▼
      DEV     QA    UAT
                     │
                     ▼
                    PROD
```

The senior-level objective is:

> **Make source changes traceable, integration predictable, builds reproducible, deployments controlled, and recovery possible.**

That is the mindset interviewers are looking for at the **15-year DevOps level**.
