# Git Basics

A beginner-friendly guide to understanding how Git works and the core commands you'll use every day. Pair this with `git-file-structure.png` (or the image in this folder) for a visual of what's described below.

## What is Git?

Git is a **Version Control System (VCS)**. It tracks changes to your files over time, so you can:

- See the full history of a project
- Go back to an older version if something breaks
- Work on new features without touching the stable code (branching)
- Collaborate with other people on the same codebase without overwriting each other's work

Git is **distributed**, meaning every developer has a full copy of the project's history on their own machine — not just on a central server.

## The Three Areas of a Git Project

Every Git-tracked project has three main areas your files move through:

1. **Working directory** — the actual files on your computer that you open and edit.
2. **Staging area (the index)** — a holding area where you place changes you want to include in your next commit.
3. **Repository (`.git` folder)** — where Git permanently stores the history of committed snapshots.

A file typically moves like this:

```
Working directory  --(git add)-->  Staging area  --(git commit)-->  Repository
```

## What's Inside the `.git` Folder

When you run `git init`, Git creates a hidden `.git` folder. This is the actual database of your repository. Key parts:

| Item | Purpose |
|---|---|
| `HEAD` | A pointer to the branch you currently have checked out |
| `config` | Settings specific to this repository (remotes, user info, etc.) |
| `refs/` | Stores pointers to commits — branches live in `refs/heads`, tags in `refs/tags` |
| `objects/` | The actual content store — every commit, file, and folder snapshot is saved here as an object |
| `index` | The binary file that backs the staging area |

You never need to touch these files directly — Git commands manage them for you — but knowing they exist helps demystify what Git is actually doing.

## Core Git Commands

### Setting up
```bash
git init                  # Turn the current folder into a git repository
git clone <url>            # Copy an existing remote repository to your machine
```

### Checking status and history
```bash
git status                 # See what's changed, staged, or untracked
git log                    # View commit history
git diff                   # See line-by-line changes not yet staged
git diff --staged          # See staged changes before committing
git diff HEAD              # See all current changes compared with the latest commit
```

`git diff` only shows changes in your working directory that are **not staged yet**. If you already used `git add`, those changes moved into the staging area, so plain `git diff` may show nothing.

To compare your current project with the latest commit, use:

```bash
git diff HEAD
```

This shows both staged and unstaged changes compared with the latest commit.

Common diff commands:

| Command | What it shows |
|---|---|
| `git diff` | Unstaged changes only |
| `git diff --staged` | Staged changes only |
| `git diff HEAD` | All current changes compared with the latest commit |
| `git diff HEAD~1 HEAD` | Difference between the latest commit and the commit before it |

### Saving changes
```bash
git add <file>              # Stage a specific file
git add .                   # Stage everything in the current folder
git commit -m "message"     # Save staged changes as a new snapshot
```

### Branching
```bash
git branch                  # List branches
git branch <name>           # Create a new branch
git checkout <name>         # Switch to a branch
git checkout -b <name>      # Create and switch in one step
git switch <name>            # Modern alternative to checkout for switching branches
```

### Merging
```bash
git merge <branch>           # Merge another branch into your current branch
```

### Working with remotes (e.g. GitHub)
```bash
git remote add origin <url>  # Link a local repo to a remote one
git push origin <branch>     # Upload local commits to the remote
git pull origin <branch>     # Download and merge remote changes into local
git fetch                    # Download remote changes without merging
```

### Undoing things
```bash
git restore <file>           # Discard changes in working directory
git reset <file>             # Unstage a file
git revert <commit>          # Create a new commit that undoes a previous one
git reset --hard <commit>    # Move branch to an old commit and delete current local changes
```

### Revert vs Reset: Are Old Commits Still Saved?

Suppose your commit history looks like this:

```text
10 -> 11 -> 12 -> 13 -> 14
```

If you go back to commit `10` and then create a new commit `15`, what happens to commits `11` to `14` depends on the command you use.

#### Using `git revert`

```bash
git revert <commit>
```

`git revert` is the safer way to undo a commit. It does not delete old commits. Instead, it creates a new commit that reverses the changes from an earlier commit.

Your history still keeps the commits in between:

```text
10 -> 11 -> 12 -> 13 -> 14 -> 15
```

So commits `11`, `12`, `13`, and `14` are still saved in normal Git history.

Example:

```bash
git revert 12
```

This creates a new commit that undoes the changes introduced by commit `12`. Commit `12` still remains in the history.

Use `git revert` when:

- The commit was already pushed to GitHub
- Other people may already have the commit
- You want a clear history that shows what was undone

#### Using `git reset --hard`

```bash
git reset --hard <commit-10>
```

There is no usual command called `git revert --hard`. When people say "hard revert", they usually mean `git reset --hard`.

`git reset --hard` is a stronger command. It moves your current branch back to an older commit and also changes your files to match that old commit exactly.

That means it can remove:

- Commits from the visible branch history
- Staged changes
- Unstaged changes in tracked files

If you run:

```bash
git reset --hard 10
```

your branch moves back to commit `10`. After that, if you make a new commit, the visible branch history may look like this:

```text
10 -> 15
```

Commits `11`, `12`, `13`, and `14` are no longer shown in the normal branch history. Git may still keep them internally for some time, and you can often find them with:

```bash
git reflog
```

But they are not guaranteed to stay forever if no branch, tag, or reflog points to them.

Use `git reset --hard` when:

- You are working locally
- You have not pushed those commits yet
- You are sure you do not need the current file changes

Do not use `git reset --hard` casually on shared commits, because it rewrites the visible branch history and can confuse other people working on the same branch.

#### Soft, Mixed, and Hard Reset

`git reset` has different levels:

| Command | What happens to commits? | What happens to file changes? |
|---|---|---|
| `git reset --soft <commit>` | Moves branch back | Keeps changes staged |
| `git reset <commit>` | Moves branch back | Keeps changes but unstages them |
| `git reset --hard <commit>` | Moves branch back | Deletes tracked file changes |

Example:

```bash
git reset --soft HEAD~1
```

This removes the latest commit from the visible branch history, but keeps that commit's changes staged so you can commit them again.

```bash
git reset HEAD~1
```

This removes the latest commit from the visible branch history, but keeps the file changes in your working directory.

```bash
git reset --hard HEAD~1
```

This removes the latest commit from the visible branch history and also removes the file changes from your working directory.

#### Quick Difference

| Command | Deletes old commits from normal history? | Creates a new commit? | Safe for shared history? |
|---|---|---|---|
| `git revert <commit>` | No | Yes | Yes |
| `git reset --hard <commit>` | Yes, from the current branch view | No | Usually no |

**Simple rule:** use `git revert` for safer undoing, especially when commits were already pushed to GitHub or shared with other people. Use `git reset --hard` carefully because it rewrites the visible branch history and discards current local changes.

## A Typical Daily Workflow

1. `git pull` — get the latest changes from the team
2. Make changes to your files
3. `git status` — check what changed
4. `git add .` — stage the changes
5. `git commit -m "describe what you did"` — save a snapshot
6. `git push` — upload your commits

## Key Terms Glossary

- **Repository (repo)** — a project tracked by Git
- **Commit** — a saved snapshot of your project at a point in time
- **Branch** — an independent line of development
- **Merge** — combining changes from one branch into another
- **Remote** — a version of your repository hosted elsewhere (e.g. GitHub)
- **Clone** — a local copy of a remote repository
- **Origin** — the default name for the remote repository you cloned from
- **HEAD** — a pointer to the commit/branch you're currently on
- **Conflict** — when Git can't automatically merge two changes to the same line of a file
