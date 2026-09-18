# Git Advanced Concepts — Senior DevOps Engineer

For a **Senior DevOps Engineer**, don't learn these as definitions only. For every topic, know:

**what it is → what happens internally → when to use it → production impact → common mistake → interview scenario.**

---

## 1. Merge vs Rebase

### Merge

Suppose:

```text
A---B---C          main
     \
      D---E        feature
```

Run:

```bash
git switch main
git merge feature
```

Result:

```text
A---B---C---------M
     \           /
      D---E------
```

`M` is a merge commit.

### Rebase

```bash
git switch feature
git rebase main
```

Result:

```text
A---B---C---D'---E'
```

Git takes `D,E`, reapplies them on top of `C`.

### Critical difference

| Merge                              | Rebase                             |
| ---------------------------------- | ---------------------------------- |
| Preserves existing history         | Rewrites commit history            |
| Can create merge commit            | Usually produces linear history    |
| Doesn't change existing commit IDs | Recreated commits get new IDs      |
| Safe for shared history            | Best suited to history you control |
| Can show actual branch topology    | Produces cleaner linear history    |

### Senior answer

> I use rebase primarily to update/clean up a private or controlled feature branch. I avoid rebasing shared history because it changes commit IDs and can disrupt other developers.

---

# 2. Reset vs Revert

This is a **must-know production concept**.

### Reset

```bash
git reset --hard HEAD~1
```

Moves the branch reference backward.

```text
Before:

A---B---C
        ↑
       main

After:

A---B
    ↑
   main
```

### Revert

```bash
git revert C
```

Creates a new commit that reverses C.

```text
A---B---C---R
            ↑
           main
```

### Key difference

```text
reset  → move/rewrite history
revert → create new history
```

### Production example

Bad commit already merged into `main`:

```bash
git revert <bad-commit>
```

Usually safer than:

```bash
git reset --hard
git push --force
```

because other developers and CI/CD may already depend on the published history.

---

# 3. Fetch vs Pull

### Fetch

```bash
git fetch origin
```

Downloads remote changes but doesn't merge them into your current branch.

```text
Remote
  │
  │ fetch
  ▼
origin/main
```

Your `main` isn't automatically changed.

You can inspect:

```bash
git log main..origin/main
```

Then decide what to do.

### Pull

Conceptually:

```bash
git pull
```

means:

```text
fetch
+
integrate
```

Integration can involve merge or rebase depending on configuration/options.

For example:

```bash
git pull --rebase
```

### Senior preference

For troubleshooting or controlled workflows:

```bash
git fetch
```

is often preferable because it separates:

```text
download
```

from:

```text
integration
```

---

# 4. HEAD vs Branch

### Branch

A branch is essentially a **reference pointing to a commit**.

```text
main
 ↓
 C
 ↓
 B
 ↓
 A
```

### HEAD

`HEAD` identifies your current position.

Normally:

```text
HEAD
 ↓
main
 ↓
C
```

So:

```text
HEAD → branch → commit
```

Check:

```bash
git symbolic-ref HEAD
```

or:

```bash
git rev-parse HEAD
```

### Detached HEAD

If you checkout a commit directly:

```bash
git checkout abc123
```

you can get:

```text
HEAD
 ↓
abc123
```

rather than:

```text
HEAD
 ↓
main
 ↓
abc123
```

That's **detached HEAD**.

If you want to preserve new work:

```bash
git switch -c recovery
```

---

# 5. Local Branch vs Remote-Tracking Branch

Suppose:

```text
main
origin/main
```

These are different references.

### Local branch

```text
main
```

exists in your local repository.

### Remote-tracking branch

```text
origin/main
```

is your local record of the remote repository's branch state.

Example:

```text
Local:
main ───────────► C

Remote tracking:
origin/main ────► D
```

You can be behind the remote.

```bash
git fetch origin
```

updates your remote-tracking references.

### Important misconception

`origin/main` is **not the live remote branch itself**.

It's your local representation of what you last fetched from that remote.

---

# 6. Merge Conflict vs Rebase Conflict

## Merge conflict

```bash
git merge feature
```

Git can't automatically combine changes.

You resolve:

```bash
git add <file>
git commit
```

### Rebase conflict

```bash
git rebase main
```

Git is replaying your commits and one of them conflicts.

Resolve:

```bash
git add <file>
git rebase --continue
```

Or abandon:

```bash
git rebase --abort
```

### Conceptual difference

```text
Merge:
combine histories

Rebase:
replay commits
```

Therefore a rebase can produce conflicts **one commit at a time**.

### Senior troubleshooting

Don't blindly resolve conflicts.

First determine:

```text
What was the original intent?
What changed in main?
What was changed in feature?
Is the feature still valid?
Could the change be redesigned instead?
```

---

# 7. Cherry-Pick vs Merge

### Merge

Brings the branch histories together.

```text
main
  \
   feature
```

```bash
git merge feature
```

### Cherry-pick

Selects a specific commit:

```bash
git cherry-pick abc123
```

Example:

```text
feature:

A---B---C---D---E

Production:

A---B
```

You only need `D`:

```text
A---B---D'
```

### Common DevOps use case

A production hotfix exists on another branch:

```text
release/1.5
```

and you need that particular fix elsewhere.

Cherry-pick can be appropriate.

### Risk

Cherry-picking creates a new commit.

```text
D ≠ D'
```

This can complicate future merges if the same logical change is later merged through the normal branch relationship.

---

# 8. Reflog vs Log

This is a **senior troubleshooting favorite**.

### `git log`

Shows commit history reachable through references.

```bash
git log --oneline
```

Think:

> "What commits are in this branch's history?"

### `git reflog`

Shows movements of references such as `HEAD`.

```bash
git reflog
```

Think:

> "Where was my HEAD/branch pointing recently?"

### Example

You accidentally do:

```bash
git reset --hard HEAD~5
```

`git log` may no longer show those five commits from the current branch.

But:

```bash
git reflog
```

can often reveal the previous position.

Then:

```bash
git reset --hard <old-commit>
```

can recover it.

### Mental model

```text
git log
   ↓
repository history

git reflog
   ↓
reference movement history
```

---

# 9. Soft vs Mixed vs Hard Reset

Suppose:

```text
A---B---C
        ↑
       HEAD
```

You run:

```bash
git reset HEAD~1
```

The target becomes:

```text
A---B
    ↑
   HEAD
```

The difference is what happens to the **index/staging area and working tree**.

| Reset     | HEAD  | Staging   | Working files           |
| --------- | ----- | --------- | ----------------------- |
| `--soft`  | moved | preserved | preserved               |
| `--mixed` | moved | reset     | preserved               |
| `--hard`  | moved | reset     | reset/discarded changes |

### Soft

```bash
git reset --soft HEAD~1
```

Useful when you want to redo the commit.

Changes remain staged.

### Mixed

```bash
git reset --mixed HEAD~1
```

Changes remain in working directory but become unstaged.

### Hard

```bash
git reset --hard HEAD~1
```

Moves HEAD and resets tracked working-tree/index state to that commit.

**Dangerous.**

Senior engineers should understand exactly what will be lost before using it.

---

# 10. Git vs GitHub vs GitLab vs Azure Repos

This distinction is extremely important in DevOps interviews.

### Git

Version-control technology/software.

```text
git commit
git branch
git merge
git rebase
```

### GitHub

A platform built around Git repositories and collaboration.

Common features:

```text
Repositories
Pull Requests
Actions
Branch protection
Code review
Security scanning
Packages
```

### GitLab

Git-based DevOps platform.

Common areas:

```text
Repository
Merge Requests
CI/CD
Container Registry
Security
Deployments
```

### Azure Repos

Microsoft Azure DevOps service for Git repositories.

Often integrated with:

```text
Azure Pipelines
Azure Boards
Azure Artifacts
Azure Test Plans
```

### Mental model

```text
                    Git
                     │
       Version control technology
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
     GitHub        GitLab      Azure Repos
        │            │            │
      PRs          MRs          PRs
      Actions      CI/CD       Pipelines
```

Git itself does not provide the complete enterprise DevOps platform.

---

# 11. Git Hooks vs CI/CD Checks

## Git Hook

Runs at a Git lifecycle event.

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
    ↓
pre-commit
    ↓
lint/test
    ↓
commit
```

Useful for fast developer feedback.

---

## CI/CD Checks

Run centrally on the CI/CD platform.

Example:

```text
Push
 ↓
Pipeline
 ↓
Build
 ↓
Unit tests
 ↓
SAST
 ↓
Dependency scan
 ↓
Secret scan
 ↓
PR approval
```

### Critical difference

A local Git hook can potentially be bypassed.

Therefore:

```text
Local hook
    ↓
Developer convenience
```

while:

```text
CI/CD policy
    ↓
Central enforcement
```

For enterprise governance, don't rely solely on local hooks.

---

# 12. Git LFS vs Normal Git

### Normal Git

Designed primarily around source-code-sized files and repository history.

Example:

```text
.py
.yaml
.json
.tf
.sh
.md
```

### Git LFS

Large File Storage.

Useful for:

```text
large binaries
datasets
videos
media
large model files
```

Conceptually:

```text
Git repository
      │
      └── LFS pointer
               │
               ▼
          LFS storage
               │
               ▼
         actual large file
```

### Why not put huge binaries directly in Git?

Git history is persistent.

If you commit a 2 GB file and later delete it, the object may still exist in repository history until properly removed/garbage-collected according to the hosting/storage lifecycle.

Large files can therefore make repositories:

```text
slow to clone
large to store
expensive to maintain
```

---

# 13. Shallow Clone vs Full Clone

### Full clone

```bash
git clone <repo>
```

Downloads repository history needed for a normal complete clone.

Useful when you need:

```text
full history
bisect
history analysis
some advanced tooling
```

### Shallow clone

```bash
git clone --depth 1 <repo>
```

Gets limited history.

Conceptually:

```text
Full:

A---B---C---D---E---F---G

Shallow:

                  E---F---G
```

Useful in CI/CD when you only need the current source.

### CI/CD benefit

Potentially reduces:

```text
clone time
network transfer
storage
```

But shallow history can break workflows that depend on historical commits.

For example:

```bash
git bisect
```

may require additional history.

You can deepen it:

```bash
git fetch --deepen=50
```

or convert to full history:

```bash
git fetch --unshallow
```

---

# Senior DevOps Decision Matrix

| Situation                       | Typical consideration  |
| ------------------------------- | ---------------------- |
| Clean private feature history   | Rebase                 |
| Shared branch history           | Avoid rewriting        |
| Undo published bad commit       | Revert                 |
| Move local branch backward      | Reset                  |
| Inspect remote changes safely   | Fetch                  |
| Fetch + integrate               | Pull                   |
| Recover lost local commit       | Reflog                 |
| Bring one specific fix          | Cherry-pick            |
| Combine complete branch history | Merge                  |
| Large binary assets             | Git LFS                |
| Fast CI checkout                | Shallow clone          |
| Central quality enforcement     | CI/CD                  |
| Developer-side fast feedback    | Git hooks              |
| Current position                | HEAD                   |
| Local development branch        | Local branch           |
| Local view of remote branch     | Remote-tracking branch |

## The 12-year interview mindset

When asked **"Which one would you use?"**, don't answer only with the command.

Use this decision flow:

```text
                    Git problem
                         │
             ┌───────────┴───────────┐
             │                       │
       Is history shared?       Is history private?
             │                       │
            YES                      YES
             │                       │
      Avoid rewriting            Rebase may be OK
             │
             ▼
     Is change already public?
             │
       ┌─────┴─────┐
      YES           NO
       │             │
    Revert       Reset/Rebase
```

And for production:

```text
          Change needed
                │
                ▼
       Is it one specific commit?
          │              │
         YES             NO
          │              │
    Cherry-pick        Merge/Rebase
          │              │
          ▼              ▼
   Check dependencies   Check branch policy
                │
                ▼
             CI/CD
                │
                ▼
             Review
                │
                ▼
           Deployment
```

**The senior-level skill isn't memorizing Git commands. It's understanding how a Git operation changes history, what other engineers/CI pipelines will see, and whether the operation is reversible and safe in a shared production environment.**
