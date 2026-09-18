# Git and GitHub Materials

Practical notes for learning Git as a DevOps engineer. This collection moves from the Git object model and everyday workflow to collaboration strategies, history management, commit conventions, and interview preparation.

## Contents

| Document | What it covers |
| --- | --- |
| [Git Basic and Advanced](git-basic-advanced.md) | Git vs. GitHub, the working tree, staging area, repositories, commits, branches, `HEAD`, and Git's object model. |
| [Git Commits](git_commits.md) | Conventional Commits, commit types, scopes, breaking changes, and commit quality for release automation and auditability. |
| [Branching Strategies](git_branching_strategies.md) | Feature branching, GitHub Flow, Git Flow, trunk-based development, release and environment branches, and how to choose a strategy. |
| [Advanced Git Practices](git-advanced-practices.md) | Merge vs. rebase, reset vs. revert, fetch vs. pull, branch references, and production-safe history management. |
| [Interview Q&A](Interview_QAs.md) | Senior-level Git and GitHub interview questions covering fundamentals, collaboration, troubleshooting, and operational scenarios. |

## Recommended Learning Path

1. Start with [Git Basic and Advanced](git-basic-advanced.md) to understand how Git stores and moves changes.
2. Read [Git Commits](git_commits.md) and practice writing consistent commit messages.
3. Compare the workflows in [Branching Strategies](git_branching_strategies.md) and relate them to release cadence and CI/CD needs.
4. Use [Advanced Git Practices](git-advanced-practices.md) to reason about rewriting history, recovery, and safely integrating remote changes.
5. Review [Interview Q&A](Interview_QAs.md) to test your understanding and prepare for senior DevOps discussions.

## Core Workflow

The materials repeatedly build on this model:

```text
Working directory
	|
	| git add
	v
Staging area
	|
	| git commit
	v
Local repository
	|
	| git push / git fetch
	v
Remote repository
```

Useful commands to practice while reading:

```bash
git status
git log --oneline --graph --decorate
git switch -c feature/my-change
git add <file>
git commit -m "docs: explain Git workflow"
git fetch origin
git push -u origin feature/my-change
```

## Intended Audience

These notes are written for learners building practical Git skills and for DevOps engineers preparing for senior-level conversations about:

- Distributed version control and Git internals
- Pull-request collaboration and branch protection
- Commit history, releases, and auditability
- Conflict resolution and safe recovery
- Branching strategy and CI/CD integration

The examples are educational. Before running commands such as `reset --hard`, `rebase`, or force-push operations, verify the branch and confirm whether its history is shared.
