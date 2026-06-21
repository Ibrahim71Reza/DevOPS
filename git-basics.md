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
```

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
```

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

