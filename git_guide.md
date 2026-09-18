# Git — Core Concepts for Senior DevOps

## 1. What is Git?

**Git is a distributed version control system (DVCS)** used to track changes in source code and collaborate across teams.

The key word is **distributed**:

```text
                 Remote Repository
                    GitHub/GitLab
                         │
              ┌──────────┴──────────┐
              │                     │
        Developer A            Developer B
        Local Git Repo         Local Git Repo
              │                     │
          Working Tree          Working Tree
```

Each developer has a **complete Git repository**, including its history.

### Git vs GitHub

| Git                    | GitHub                             |
| ---------------------- | ---------------------------------- |
| Version control system | Git hosting/collaboration platform |
| Runs locally           | Cloud/service                      |
| Tracks history         | Hosts repositories                 |
| `git commit`           | Pull Requests                      |
| `git branch`           | Code Reviews                       |
| `git merge`            | CI/CD integrations                 |

**Important interview point:** Git does not require GitHub. You can use Git completely locally or with another remote server.

---

# 2. Git Architecture

Understand these four areas:

```text
Working Directory
       │
       │ git add
       ▼
Staging Area
       │
       │ git commit
       ▼
Local Repository
       │
       │ git push
       ▼
Remote Repository
```

### Working Directory

Your actual files.

```text
app.py
Dockerfile
README.md
```

You modify files here.

### Staging Area

Also called the **index**.

```bash
git add app.py
```

You are telling Git:

> "Include this particular version of app.py in my next commit."

### Local Repository

Contains committed history.

```bash
git commit -m "Add authentication"
```

### Remote Repository

For example:

```text
GitHub
GitLab
Azure Repos
Bitbucket
```

You synchronize using:

```bash
git push
git fetch
git pull
```

---

# 3. Git Object Model

This becomes important at senior level.

Git fundamentally stores objects.

```text
Git Objects
│
├── Blob
├── Tree
├── Commit
└── Tag
```

## Blob

Stores **file contents**.

It doesn't primarily store the filename.

```text
Blob
 └── file content
```

## Tree

Represents directory structure and points to blobs/other trees.

```text
Tree
├── app.py → Blob
├── config.yaml → Blob
└── src/ → Tree
```

## Commit

A commit points to a tree and parent commit(s).

```text
Commit
│
├── Tree
├── Parent
├── Author
├── Committer
└── Message
```

Example:

```text
A → B → C → D
```

Commit D points to C.

---

# 4. SHA-1 / Git Object Hash

Git historically identifies objects using SHA-1 hashes.

Example:

```text
a84c3f7e8b...
```

The hash is derived from the object's content.

Therefore:

```text
same content → same hash
different content → different hash
```

Modern Git also supports **SHA-256 repositories**, but SHA-1 remains common in existing repositories.

### Why does this matter?

It provides content-addressability and helps Git detect changes and maintain object integrity.

---

# 5. Commit

A commit represents a snapshot of the repository.

```bash
git commit -m "Fix deployment script"
```

Conceptually:

```text
A
│
▼
B
│
▼
C
```

Each commit contains metadata and references its parent.

A commit is **not simply a diff file**. Git's model is snapshot-oriented, although Git can efficiently derive/display differences between snapshots.

---

# 6. HEAD

`HEAD` represents your current position in the repository.

Usually:

```text
HEAD
 │
 ▼
main
 │
 ▼
commit C
```

Check it:

```bash
git status
```

or:

```bash
git rev-parse HEAD
```

---

# 7. Branch

A Git branch is essentially a **movable reference/pointer to a commit**.

Suppose:

```text
A → B → C
        ↑
       main
```

Create a branch:

```bash
git branch feature/login
```

Now:

```text
        feature/login
             ↓
A → B → C
        ↑
       main
```

After a new commit:

```text
        feature/login
             ↓
A → B → C → D
        ↑
       main
```

The branch itself does not contain all the files.

It is a reference pointing to a commit.

---

# 8. Checkout vs Switch

Older/common command:

```bash
git checkout feature/login
```

Modern Git:

```bash
git switch feature/login
```

Create and switch:

```bash
git switch -c feature/login
```

`git switch` is clearer because it is specifically intended for branch switching.

---

# 9. Merge

Suppose:

```text
main:
A → B → C

feature:
     B → D → E
```

Merge:

```bash
git switch main
git merge feature
```

Potential result:

```text
A → B → C ───── M
     \         /
      D → E ──
```

`M` is a **merge commit**.

---

# 10. Fast-Forward Merge

If main hasn't moved:

```text
A → B → C
         \
          D → E
```

Main can simply move forward:

```text
A → B → C → D → E
```

This is a **fast-forward merge**.

```bash
git merge feature
```

Force a merge commit:

```bash
git merge --no-ff feature
```

---

# 11. Rebase

Rebase moves/replays your commits onto another base.

Before:

```text
A → B → C       main
     \
      D → E     feature
```

After:

```text
A → B → C → D' → E'
```

Your commits get **new commit identities**.

### Critical rule

Avoid rebasing commits that other people are already depending on, unless your team explicitly handles history rewriting.

---

# 12. Merge vs Rebase

| Merge                            | Rebase                              |
| -------------------------------- | ----------------------------------- |
| Preserves branch history         | Creates linear history              |
| Can create merge commit          | Replays commits                     |
| Doesn't rewrite existing commits | Changes commit IDs                  |
| Safer for shared branches        | Useful for private feature branches |
| History can become complex       | History can be cleaner              |

Senior-level answer:

> "I don't choose merge vs rebase simply based on preference. I consider whether the commits are already shared, the team's branching policy, auditability, and the desired repository history."

---

# 13. `git pull`

A very important interview question.

Normally:

```bash
git pull
```

is essentially:

```bash
git fetch
git merge
```

depending on configuration.

You can also configure pull to rebase:

```bash
git pull --rebase
```

So:

```text
Remote
  │
  │ fetch
  ▼
Remote-tracking branch
  │
  │ merge/rebase
  ▼
Current branch
```

---

# 14. `git fetch`

```bash
git fetch origin
```

Downloads changes from remote but **doesn't modify your current working branch**.

This makes it safer for inspecting remote changes.

```bash
git fetch
git log HEAD..origin/main
```

---

# 15. Remote-Tracking Branch

Example:

```text
origin/main
```

This is **not your local main branch**.

You might have:

```text
main
origin/main
```

Conceptually:

```text
Local:
main ──────────────► C

Remote tracking:
origin/main ───────► D
```

Your local `main` can be behind `origin/main`.

---

# 16. `origin`

When you clone:

```bash
git clone https://...
```

Git commonly creates a remote named:

```text
origin
```

Check:

```bash
git remote -v
```

You can have multiple remotes:

```text
origin
upstream
```

This is common in fork-based workflows.

---

# 17. Git Revert vs Reset

One of the most important DevOps interview topics.

## Revert

```bash
git revert <commit>
```

Creates a **new commit that reverses an earlier commit**.

```text
A → B → C → D
          ↓
          E
```

Good for shared branches.

### Production example

A bad deployment change was merged into `main`.

Instead of rewriting history:

```bash
git revert <bad-commit>
git push
```

CI/CD sees a new commit and can deploy the correction.

---

# 18. Reset

```bash
git reset
```

Moves the current branch pointer.

Three important modes:

```bash
git reset --soft
git reset --mixed
git reset --hard
```

### Soft

```bash
git reset --soft HEAD~1
```

Moves HEAD but keeps changes staged.

### Mixed

```bash
git reset --mixed HEAD~1
```

Moves HEAD and unstages changes.

This is generally the default reset mode.

### Hard

```bash
git reset --hard HEAD~1
```

Moves HEAD and discards working/staged changes associated with the reset.

**Dangerous command.**

---

# 19. Reset vs Revert

```text
RESET
changes history

REVERT
adds a new commit
```

For shared production branches:

```text
Usually prefer:
git revert
```

rather than rewriting published history.

---

# 20. Cherry-Pick

Suppose:

```text
main:
A → B → C

feature:
     \
      D → E
```

You only want D.

```bash
git cherry-pick <D>
```

Result:

```text
A → B → C → D'
```

Useful for:

* hotfixes
* backporting
* selected commits
* release branches

But cherry-picking can create duplicate logical changes and conflicts if the same change is later merged another way.

---

# 21. Stash

Temporarily save uncommitted work.

```bash
git stash
```

Then:

```bash
git pull
```

Restore:

```bash
git stash pop
```

Better descriptive approach:

```bash
git stash push -m "WIP login changes"
```

List:

```bash
git stash list
```

---

# 22. Tags

Tags identify important commits.

```bash
git tag v1.0.0
```

Typical DevOps use:

```text
v1.0.0
v1.1.0
v2.0.0
```

CI/CD can use tags to trigger releases:

```text
git tag
   ↓
CI Pipeline
   ↓
Build
   ↓
Test
   ↓
Docker image
   ↓
Production
```

Annotated tag:

```bash
git tag -a v1.0.0 -m "Production release 1.0.0"
```

---

# 23. `.gitignore`

Controls files Git should not normally track.

Example:

```gitignore
*.log
.env
.terraform/
*.tfstate
__pycache__/
.vscode/
```

Important DevOps rule:

**Never commit secrets simply because `.gitignore` exists.**

If a secret was already committed, adding it to `.gitignore` does not remove it from Git history.

---

# 24. Git Conflict

A conflict occurs when Git cannot automatically reconcile changes.

Example:

```text
main:
A → B → C

feature:
     B → D
```

Both modify the same lines.

During merge:

```text
<<<<<<< HEAD
production_value
=======
development_value
>>>>>>> feature
```

Resolve manually:

```bash
git add <file>
git commit
```

For rebase:

```bash
git add <file>
git rebase --continue
```

Abort:

```bash
git merge --abort
```

or:

```bash
git rebase --abort
```

---

# 25. Three-Way Merge

Senior-level concept.

Git generally compares:

```text
          Base
         /    \
      Ours    Theirs
```

rather than simply comparing two files.

This allows Git to determine:

* what changed on our side
* what changed on their side
* what existed in the common ancestor

Understanding the **merge base** is important when troubleshooting complex conflicts.

---

# 26. Git Reflog

One of the most valuable recovery mechanisms.

```bash
git reflog
```

It records movements of references such as `HEAD`.

Example:

```text
HEAD@{0}
HEAD@{1}
HEAD@{2}
```

Suppose you accidentally do:

```bash
git reset --hard HEAD~5
```

and lose your visible branch history.

You can often find the previous commit through:

```bash
git reflog
```

then recover:

```bash
git reset --hard <commit>
```

### Senior DevOps interview point

> Reflog is local and is not a replacement for a remote backup.

---

# 27. `git bisect`

Used to identify which commit introduced a bug.

```bash
git bisect start
git bisect bad
git bisect good <known-good-commit>
```

Git performs a binary search through commits.

For a history of 1,024 commits:

```text
1024
 ↓
512
 ↓
256
 ↓
128
...
```

Approximately:

```text
log₂(1024) = 10
```

tests can identify the problematic commit.

Very useful in production troubleshooting.

---

# 28. Git Blame

```bash
git blame app.py
```

Shows which commit last modified each line.

Better use:

```bash
git blame -L 20,40 app.py
```

Important distinction:

> `git blame` tells you who last changed a line, not necessarily who is responsible for introducing the bug.

---

# 29. Git Diff

Working tree changes:

```bash
git diff
```

Staged changes:

```bash
git diff --cached
```

Compare branches:

```bash
git diff main..feature
```

Compare commits:

```bash
git diff HEAD~1 HEAD
```

---

# 30. Git Log

Basic:

```bash
git log
```

Useful senior-level view:

```bash
git log --oneline --graph --decorate --all
```

Example:

```text
* 91ac2f1 (HEAD -> main) Fix pipeline
* 7db91a2 Add Dockerfile
| * 32af912 Feature authentication
|/
* 812abc1 Initial commit
```

This is one of the first commands I would use while troubleshooting a repository.

---

# 31. Git Hooks

Hooks allow scripts to run at Git lifecycle events.

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
lint
    ↓
unit tests
    ↓
commit
```

Important distinction:

**Client-side hooks can be bypassed**, so they should not be the only security/control mechanism.

For enterprise enforcement, use server-side controls and CI/CD.

---

# 32. Git in DevOps CI/CD

Typical architecture:

```text
Developer
   │
   ▼
Git Push
   │
   ▼
GitHub / GitLab / Azure Repos
   │
   ▼
Webhook / Pipeline Trigger
   │
   ▼
CI Pipeline
   │
   ├── Lint
   ├── Unit Test
   ├── Security Scan
   ├── Build
   └── Package
          │
          ▼
      Artifact
          │
          ▼
       Deploy
```

Git is therefore not merely a developer tool.

It becomes the **source-of-truth trigger for infrastructure and application delivery**.

---

# 33. Git Branching Strategies

Common strategies:

### Git Flow

```text
main
 │
develop
 │
feature/*
release/*
hotfix/*
```

### Trunk-Based Development

```text
main
 │
 ├── short-lived branch
 │
 └── short-lived branch
```

Frequent integration into main.

### Environment branches

Some organizations use:

```text
dev
qa
uat
prod
```

But this can introduce synchronization and promotion complexity.

For senior DevOps interviews, don't just define these strategies. Be prepared to discuss **trade-offs, CI/CD implications, release management, rollback, and governance**.

---

# 34. Protected Branches

Enterprise repositories commonly protect:

```text
main
release/*
```

Typical policies:

```text
Direct push ❌
Pull Request ✅
Minimum reviewers
Required CI checks
Security scans
Status checks
Signed commits (where required)
```

This prevents a developer from simply doing:

```bash
git push origin main
```

and bypassing the delivery controls.

---

# 35. Git Security

Important senior-level topics:

### Never commit

```text
passwords
API keys
private keys
cloud credentials
.env secrets
tokens
```

Use:

```text
Azure Key Vault
AWS Secrets Manager
HashiCorp Vault
GitHub/GitLab secret management
```

Also consider:

```text
secret scanning
dependency scanning
SAST
commit signing
branch protection
least privilege
```

---

# 36. Git LFS

Git LFS = **Large File Storage**.

Useful for large binary files:

```text
.iso
.psd
.large datasets
.media files
```

Instead of storing the complete large file directly in normal Git objects, Git stores a pointer while the actual content is managed by LFS storage.

---

# 37. Shallow Clone

For CI/CD you may not need complete history.

```bash
git clone --depth 1 <repo>
```

This reduces:

```text
clone time
network usage
disk usage
```

But some operations requiring history may not work normally.

For example, tools depending on complete history may require:

```bash
git fetch --unshallow
```

---

# 38. Sparse Checkout

Useful for very large monorepos when you only need part of the repository.

Conceptually:

```text
Monorepo
├── application-A
├── application-B
├── infrastructure
├── documentation
└── tools
```

You may only checkout:

```text
application-A
```

This can reduce local workspace size.

---

# 39. Git Worktree

Very useful advanced concept.

Instead of repeatedly switching branches, you can have multiple working directories backed by the same repository.

Example:

```text
repo/
repo-feature/
repo-hotfix/
```

Commands:

```bash
git worktree add ../hotfix hotfix/1.2
```

Useful when you are:

* developing a feature
* simultaneously handling a production hotfix
* working with multiple branches

---

# 40. Detached HEAD

Example:

```bash
git checkout <commit>
```

You may get:

```text
HEAD detached at abc123
```

Meaning:

```text
HEAD → commit
```

rather than:

```text
HEAD → branch → commit
```

If you create commits there and don't create a branch/reference, they can eventually become difficult to find.

Create a branch if you want to retain the work:

```bash
git switch -c recovery-branch
```

---

# 41. Senior DevOps Git Mental Model

Remember this:

```text
                    Git
                     │
       ┌─────────────┼─────────────┐
       │             │             │
    Objects       Refs          Working Tree
       │             │
 ┌─────┼─────┐       │
Blob Tree Commit   Branch
                 Tag
                 HEAD
                 Reflog
```

And the normal workflow:

```text
             git add
Working ───────────────► Staging
                           │
                           │ git commit
                           ▼
                     Local Repository
                           │
                    git push / fetch
                           │
                           ▼
                    Remote Repository
```

---

# 42. Commands You Should Know at experience Level

Don't just memorize these—understand **why and when** you use them.

```bash
git clone
git init
git status
git add
git commit
git push
git fetch
git pull
git branch
git switch
git merge
git rebase
git cherry-pick
git revert
git reset
git stash
git tag
git log
git diff
git show
git blame
git bisect
git reflog
git remote
git config
git clean
git restore
git worktree
git ls-files
git rev-parse
```

## The most important distinction set

For a senior DevOps interview, be particularly strong on:

```text
merge vs rebase
reset vs revert
fetch vs pull
HEAD vs branch
local branch vs remote-tracking branch
merge conflict vs rebase conflict
cherry-pick vs merge
reflog vs log
soft vs mixed vs hard reset
Git vs GitHub/GitLab/Azure Repos
Git hooks vs CI/CD checks
Git LFS vs normal Git
shallow clone vs full clone
```

### One senior-level principle

If an interviewer asks **"How do you recover from a bad Git operation?"**, don't immediately say `reset --hard`.

Think in terms of:

```text
What was changed?
        ↓
Is the commit already shared?
        ↓
Is history rewriting acceptable?
        ↓
Can reflog locate the previous state?
        ↓
Should I revert instead?
        ↓
What will CI/CD / other developers see?
```

That **decision-making layer** is what separates basic Git knowledge from senior DevOps Git expertise.
