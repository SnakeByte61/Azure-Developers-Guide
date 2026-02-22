Here's a comprehensive guide of **BASH COMMANDS for developers to interact with GitHub**:

```bash name=github-bash-commands.md
# GitHub Bash Commands Guide for Developers

A comprehensive reference of essential BASH commands for interacting with GitHub repositories.

## Table of Contents

1. [Initial Setup](#initial-setup)
2. [Repository Operations](#repository-operations)
3. [Branch Management](#branch-management)
4. [Committing Changes](#committing-changes)
5. [Remote Operations](#remote-operations)
6. [Viewing History](#viewing-history)
7. [Stashing & Reverting](#stashing--reverting)
8. [Merging & Rebasing](#merging--rebasing)
9. [Tags](#tags)
10. [GitHub CLI (gh) Commands](#github-cli-gh-commands)
11. [SSH Key Management](#ssh-key-management)
12. [Advanced Workflows](#advanced-workflows)

---

## Initial Setup

### Configure Git User (Required)

```bash
# Set your name globally
git config --global user.name "Your Name"

# Set your email globally
git config --global user.email "your.email@example.com"

# Verify configuration
git config --global --list

# Configure for specific repository only (local)
cd /path/to/repo
git config user.name "Your Name"
git config user.email "your.email@example.com"
```

### Configure SSH Key for Authentication

```bash
# Generate SSH key (Ed25519 recommended)
ssh-keygen -t ed25519 -C "your.email@example.com"

# Or use RSA (older systems)
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# Display public key to copy to GitHub
cat ~/.ssh/id_ed25519.pub

# Test SSH connection
ssh -T git@github.com

# Expected output: "Hi username! You've successfully authenticated..."
```

### Configure Git Aliases (Shortcuts)

```bash
# Shorten common commands
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual 'log --graph --oneline --decorate --all'

# Usage: git st (instead of git status)
```

---

## Repository Operations

### Clone Repository

```bash
# Clone via SSH (recommended)
git clone git@github.com:username/repository.git

# Clone via HTTPS
git clone https://github.com/username/repository.git

# Clone with custom directory name
git clone git@github.com:username/repository.git my-project-name

# Clone specific branch only
git clone --branch develop git@github.com:username/repository.git

# Clone with limited history (faster)
git clone --depth 1 git@github.com:username/repository.git

# Clone submodules recursively
git clone --recurse-submodules git@github.com:username/repository.git
```

### Initialize Repository

```bash
# Create new repository locally
git init

# Create new repository with initial branch name
git init --initial-branch=main

# Initialize bare repository (for servers)
git init --bare project.git
```

### Check Repository Status

```bash
# View status
git status

# Short format status
git status -s

# Show untracked files in detail
git status --porcelain

# Show stash status
git status --show-stash
```

---

## Branch Management

### List Branches

```bash
# List local branches
git branch

# List all branches (local + remote)
git branch -a

# List remote branches only
git branch -r

# List branches with last commit info
git branch -v

# List branches with tracking info
git branch -vv

# List merged branches (safe to delete)
git branch --merged

# List unmerged branches
git branch --no-merged

# Search for branch by pattern
git branch --list "feature/*"
```

### Create Branches

```bash
# Create new branch (stays on current branch)
git branch feature/new-feature

# Create and switch to new branch
git checkout -b feature/new-feature

# Or using switch (Git 2.23+)
git switch -c feature/new-feature

# Create branch from specific commit
git branch feature/new-feature abc123def

# Create branch tracking remote
git branch --track local-name origin/remote-name
```

### Switch Branches

```bash
# Switch to existing branch
git checkout feature/my-feature

# Or use switch (Git 2.23+)
git switch feature/my-feature

# Switch to previous branch
git checkout -

# Create and switch to new branch
git checkout -b feature/new-feature

# Switch with uncommitted changes
git checkout --force feature/branch
```

### Rename Branches

```bash
# Rename current branch
git branch -m new-branch-name

# Rename other branch
git branch -m old-branch-name new-branch-name

# Rename and update tracking
git branch -m old-name new-name
git push origin -u new-name
git push origin --delete old-name
```

### Delete Branches

```bash
# Delete local branch (safe)
git branch -d feature/completed-feature

# Force delete local branch
git branch -D feature/abandoned-feature

# Delete remote branch
git push origin --delete feature/completed-feature

# Or use alternative syntax
git push origin :feature/completed-feature

# Delete multiple branches
git branch -d feature/branch1 feature/branch2

# Clean up deleted remote branches locally
git fetch --prune
git remote prune origin
```

---

## Committing Changes

### Stage Changes

```bash
# Stage specific file
git add path/to/file.js

# Stage all changes in directory
git add src/

# Stage all changes
git add .

# Stage with interactive selection
git add -i

# Stage changes interactively (by hunk)
git add -p

# Unstage file
git reset HEAD path/to/file.js

# Unstage all
git reset HEAD
```

### View Changes Before Committing

```bash
# View unstaged changes
git diff

# View staged changes
git diff --staged

# View changes for specific file
git diff path/to/file.js

# View changes in commits
git diff HEAD~1 HEAD

# View changes between branches
git diff main feature/my-feature

# View statistics
git diff --stat

# View word-level differences
git diff --word-diff
```

### Commit Changes

```bash
# Commit staged changes
git commit -m "feat: add user authentication"

# Commit with detailed message
git commit -m "feat: add user authentication" -m "- Implement JWT tokens\n- Add login endpoint"

# Stage and commit in one step
git commit -am "fix: resolve null pointer exception"

# Commit with no changes (for CI/CD)
git commit --allow-empty -m "ci: trigger deployment"

# Amend last commit (before pushing)
git commit --amend -m "corrected: add user authentication"

# Amend last commit without changing message
git commit --amend --no-edit

# Add files to last commit
git add forgotten-file.js
git commit --amend --no-edit
```

### View Commit History

```bash
# View commits (interactive)
git log

# View last N commits
git log -n 5

# View commits as one line per commit
git log --oneline

# View with branch graph
git log --graph --oneline --decorate --all

# View with author and date
git log --pretty=format:"%h %an %ad %s"

# View commits for specific file
git log path/to/file.js

# View commits since date
git log --since="2 weeks ago"

# View commits by author
git log --author="John Doe"

# View commits with changes
git log -p

# Search commits by message
git log --grep="authentication"

# View specific commit details
git show abc123def

# View who changed each line (blame)
git blame path/to/file.js
```

---

## Remote Operations

### Manage Remotes

```bash
# List all remotes
git remote

# List remotes with URLs
git remote -v

# Add remote
git remote add origin git@github.com:username/repo.git

# Remove remote
git remote remove origin

# Rename remote
git remote rename origin upstream

# Change remote URL
git remote set-url origin git@github.com:username/new-repo.git

# View remote details
git remote show origin

# Set tracking branch for local branch
git branch -u origin/feature/my-feature
```

### Fetch from Remote

```bash
# Fetch from default remote
git fetch

# Fetch from specific remote
git fetch origin

# Fetch all remotes
git fetch --all

# Fetch and prune deleted branches
git fetch --prune

# Fetch specific branch
git fetch origin feature/my-feature

# Fetch and checkout branch
git fetch origin
git checkout --track origin/feature/my-feature
```

### Push to Remote

```bash
# Push current branch
git push

# Push to specific remote and branch
git push origin feature/my-feature

# Push all branches
git push --all

# Push with tags
git push --tags

# Force push (use carefully!)
git push --force

# Force push safely (only if no new commits added)
git push --force-with-lease

# Delete remote branch
git push origin --delete feature/old-feature

# Push to set upstream
git push -u origin feature/my-feature

# Push multiple commits at once
git push origin develop main
```

### Pull from Remote

```bash
# Fetch and merge (standard pull)
git pull

# Fetch and rebase (cleaner history)
git pull --rebase

# Pull from specific remote
git pull origin feature/my-feature

# Pull tags
git pull --tags

# Pull without fast-forward (creates merge commit)
git pull --no-ff
```

---

## Viewing History

### View Logs and History

```bash
# Basic log
git log

# Condensed log
git log --oneline -10

# Visual tree of commits
git log --graph --oneline --all

# Log with statistics
git log --stat

# Log with changes
git log -p

# Pretty formatted log
git log --pretty=format:"%H %an %ae %ad %s" --date=short

# Search in commit messages
git log --grep="fix" --oneline

# Search in commit changes
git log -S "function_name" --oneline

# Log for specific file
git log -- path/to/file.js

# Log between dates
git log --since="2024-01-01" --until="2024-02-22"

# Log by author
git log --author="John Doe"

# Show specific commit
git show abc123def

# Show commit with changes
git show abc123def --stat

# Show file at specific commit
git show abc123def:path/to/file.js
```

### Compare Commits and Branches

```bash
# Diff between working directory and staging
git diff

# Diff between staging and last commit
git diff --staged

# Diff between two commits
git diff abc123def def456ghi

# Diff between two branches
git diff main feature/my-feature

# Diff with statistics
git diff --stat main feature/my-feature

# Diff specific file
git diff main feature/my-feature -- path/to/file.js

# Count changes
git diff --shortstat main feature/my-feature
```

---

## Stashing & Reverting

### Stash Changes

```bash
# Stash current changes
git stash

# Stash with description
git stash save "WIP: new feature"

# Stash including untracked files
git stash -u

# Stash specific files
git stash push path/to/file.js

# List all stashes
git stash list

# Apply latest stash
git stash apply

# Apply specific stash
git stash apply stash@{2}

# Apply and remove stash
git stash pop

# Remove specific stash
git stash drop stash@{2}

# Clear all stashes
git stash clear

# Create branch from stash
git stash branch new-branch
```

### Revert Changes

```bash
# Discard changes in working directory
git checkout -- path/to/file.js

# Discard all changes in working directory
git checkout -- .

# Unstage file
git reset HEAD path/to/file.js

# Reset to last commit
git reset --hard HEAD

# Reset to previous commit (keep changes)
git reset --mixed HEAD~1

# Reset to previous commit (discard changes)
git reset --hard HEAD~1

# Revert specific commit (creates new commit)
git revert abc123def

# Revert merge commit
git revert -m 1 abc123def

# Revert without editing message
git revert --no-edit abc123def
```

---

## Merging & Rebasing

### Merge Branches

```bash
# Merge feature branch into current branch
git merge feature/my-feature

# Merge with fast-forward only
git merge --ff-only feature/my-feature

# Merge without fast-forward (keeps branch history)
git merge --no-ff feature/my-feature

# Merge specific commit
git merge abc123def

# Abort merge conflict resolution
git merge --abort

# View merge status
git merge --verbose

# Merge squashing all commits
git merge --squash feature/my-feature
```

### Rebase Branches

```bash
# Rebase current branch onto another
git rebase main

# Interactive rebase (edit commits)
git rebase -i HEAD~3

# Rebase with autostash (save changes)
git rebase --autostash main

# Continue rebase after conflict
git rebase --continue

# Abort rebase
git rebase --abort

# Skip current commit in rebase
git rebase --skip

# Force push after rebase
git push --force-with-lease
```

### Handle Merge Conflicts

```bash
# Check merge status
git status

# View conflicted file
cat path/to/conflicted/file.js

# Use ours version (current branch)
git checkout --ours path/to/file.js

# Use theirs version (incoming branch)
git checkout --theirs path/to/file.js

# Manually resolve and stage
git add path/to/file.js

# Complete merge
git commit -m "Resolved merge conflict"

# Abort merge
git merge --abort
```

---

## Tags

### Create Tags

```bash
# Create lightweight tag
git tag v1.0.0

# Create annotated tag (with message)
git tag -a v1.0.0 -m "Release version 1.0.0"

# Create tag on specific commit
git tag v1.0.0 abc123def

# Create tag with GPG signature
git tag -s v1.0.0 -m "Signed release"

# Create tag from specific date
git tag v1.0.0 --format=json abc123def
```

### List and View Tags

```bash
# List all tags
git tag

# List tags matching pattern
git tag -l "v1.*"

# Show tag details
git show v1.0.0

# List tags with messages
git tag -l -n
```

### Push and Delete Tags

```bash
# Push specific tag
git push origin v1.0.0

# Push all tags
git push --tags

# Delete local tag
git tag -d v1.0.0

# Delete remote tag
git push origin --delete v1.0.0

# Or use alternative syntax
git push origin :v1.0.0
```

---

## GitHub CLI (gh) Commands

### Setup and Authentication

```bash
# Install GitHub CLI (macOS with Homebrew)
brew install gh

# Install GitHub CLI (Ubuntu/Debian)
sudo apt-get install gh

# Authenticate with GitHub
gh auth login

# Check authentication status
gh auth status

# Logout
gh auth logout

# View available commands
gh --help
```

### Repository Operations with gh

```bash
# Create new repository
gh repo create my-repo --public

# Clone repository
gh repo clone username/repository

# View repository information
gh repo view

# View repository in browser
gh repo view --web

# Fork repository
gh repo fork

# List repositories
gh repo list

# List organization repositories
gh repo list github

# Get repository clone URL
gh repo view --json url --jq .url
```

### Issue Management with gh

```bash
# List issues
gh issue list

# List issues with filter
gh issue list --state open --label "bug"

# Create issue
gh issue create --title "Bug: login fails" --body "Steps to reproduce..."

# View issue
gh issue view 123

# Close issue
gh issue close 123

# Reopen issue
gh issue reopen 123

# Add label to issue
gh issue edit 123 --add-label "priority-high"

# Comment on issue
gh issue comment 123 --body "Fixed in PR #456"

# Search issues
gh issue list --search "authentication"
```

### Pull Request Management with gh

```bash
# List pull requests
gh pr list

# List pull requests with filter
gh pr list --state open --author username

# Create pull request
gh pr create --title "Add feature" --body "Description"

# Create PR for current branch
gh pr create --fill

# View pull request
gh pr view 123

# Check out pull request
gh pr checkout 123

# Merge pull request
gh pr merge 123

# Merge with squash
gh pr merge 123 --squash

# Close pull request
gh pr close 123

# Request review on PR
gh pr review 123 --request-review username

# Approve pull request
gh pr review 123 --approve

# Leave comment on PR
gh pr comment 123 --body "LGTM!"

# Get PR status
gh pr status
```

### Branch Operations with gh

```bash
# Create branch from issue
gh issue develop 123

# Delete branch
gh repo delete-branch feature/old-feature

# Rename branch
gh api repos/OWNER/REPO/branches/OLD/rename -F new_branch_name=NEW
```

### Gist Operations with gh

```bash
# Create gist
gh gist create path/to/file.js

# List gists
gh gist list

# View gist
gh gist view GIST_ID

# Delete gist
gh gist delete GIST_ID
```

---

## SSH Key Management

### Generate and Configure SSH Keys

```bash
# Generate new SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Generate RSA key (older systems)
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# Display public key
cat ~/.ssh/id_ed25519.pub

# Copy to clipboard (macOS)
cat ~/.ssh/id_ed25519.pub | pbcopy

# Copy to clipboard (Linux)
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard

# Test SSH connection
ssh -T git@github.com

# Add SSH key to SSH agent
ssh-add ~/.ssh/id_ed25519

# List loaded SSH keys
ssh-add -l

# Use specific SSH key for repository
git config core.sshCommand "ssh -i ~/.ssh/custom_key_id_rsa"
```

---

## Advanced Workflows

### Syncing Fork with Upstream

```bash
# Add upstream remote
git remote add upstream git@github.com:original-owner/repository.git

# Verify remotes
git remote -v

# Fetch upstream changes
git fetch upstream

# Switch to main branch
git checkout main

# Merge upstream main
git merge upstream/main

# Push to your fork
git push origin main

# In one command (for develop branch)
git fetch upstream && git rebase upstream/develop
```

### Cherry-pick Commits

```bash
# Cherry-pick specific commit
git cherry-pick abc123def

# Cherry-pick multiple commits
git cherry-pick abc123def def456ghi

# Cherry-pick with edit
git cherry-pick -e abc123def

# Continue after conflict
git cherry-pick --continue

# Abort cherry-pick
git cherry-pick --abort
```

### Finding and Fixing Bugs

```bash
# Bisect to find bug-introducing commit
git bisect start
git bisect bad HEAD
git bisect good HEAD~10
# Test each version until bug is found

# End bisect
git bisect reset

# Find who introduced a change
git blame path/to/file.js

# Show all changes for a function
git log -L :function_name:path/to/file.js
```

### Clean Up Repository

```bash
# Show what would be deleted
git clean -d -n

# Remove untracked files
git clean -d -f

# Remove untracked files and ignored files
git clean -d -f -x

# Garbage collection
git gc

# Optimize repository
git maintenance run --auto

# Reflog cleanup (DANGEROUS - removes all history)
git reflog expire --expire=now --all
git gc --prune=now
```

### Squashing Commits

```bash
# Interactive rebase to squash last 3 commits
git rebase -i HEAD~3
# Change 'pick' to 'squash' for commits to squash
# Save and edit commit message

# Squash and merge (from pull request)
git merge --squash feature/my-feature
git commit -m "Merged: my feature"
```

### Working with Multiple Remote Repositories

```bash
# Push to multiple remotes
git push origin main
git push upstream main

# Create alias to push to all
git config --global alias.pushall 'push --all'

# Push to all remotes at once
git remote | xargs -I {} git push {}

# Clone from one remote and set another as upstream
git clone git@github.com:username/fork.git
cd fork
git remote add upstream git@github.com:original/repo.git
```

### Undoing Last Push

```bash
# View what will be reset
git log --oneline -5

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Force push the change
git push --force-with-lease

# Recover lost commits
git reflog
git reset --hard abc123def
```

---

## Useful Git Aliases for Daily Use

```bash
# Add these to your ~/.gitconfig or run as commands

git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual 'log --graph --oneline --decorate --all'
git config --global alias.amend 'commit --amend --no-edit'
git config --global alias.undo 'reset --soft HEAD~1'
git config --global alias.clean-branches 'branch --merged | grep -v "\*" | xargs -n 1 git branch -d'
git config --global alias.contributors 'shortlog --summary --numbered'
git config --global alias.lines 'diff --stat'
```

**Usage:**
```bash
git co feature/my-feature      # checkout
git br -a                       # branch -a
git st                          # status
git visual                      # pretty log graph
git clean-branches              # delete merged branches
```

---

## Common Workflows

### Feature Development Workflow

```bash
# 1. Create and switch to feature branch
git checkout -b feature/new-feature

# 2. Make changes and commit
git add .
git commit -m "feat: implement new feature"

# 3. Push to remote
git push -u origin feature/new-feature

# 4. Create pull request (via GitHub UI or gh)
gh pr create --title "Add new feature" --fill

# 5. Update based on review
git add .
git commit --amend --no-edit
git push --force-with-lease

# 6. Merge after approval
gh pr merge

# 7. Clean up
git checkout main
git pull origin main
git branch -d feature/new-feature
git push origin --delete feature/new-feature
```

### Bug Fix Workflow

```bash
# 1. Create bug fix branch from main
git checkout main
git pull origin main
git checkout -b bugfix/issue-123

# 2. Make fix and test
git add .
git commit -m "fix: resolve null pointer exception

Fixes #123"

# 3. Push and create PR
git push -u origin bugfix/issue-123
gh pr create --fill

# 4. Merge after approval
gh pr merge

# 5. Delete branch
git checkout main
git pull origin main
git branch -d bugfix/issue-123
```

### Syncing with Upstream

```bash
# Keep your fork updated with upstream
git fetch upstream
git rebase upstream/main
git push origin main --force-with-lease
```

---

## Quick Reference Table

| Task | Command |
|------|---------|
| Clone repo | `git clone git@github.com:user/repo.git` |
| Create branch | `git checkout -b feature/name` |
| Switch branch | `git checkout feature/name` |
| Stage changes | `git add .` |
| Commit | `git commit -m "message"` |
| Push | `git push origin feature/name` |
| Pull | `git pull` |
| View log | `git log --oneline` |
| Create tag | `git tag -a v1.0.0 -m "Release"` |
| Merge | `git merge feature/name` |
| Rebase | `git rebase main` |
| Stash | `git stash` |
| Unstage | `git reset HEAD file.js` |
| Discard | `git checkout -- file.js` |
| Undo commit | `git reset --soft HEAD~1` |
| View diff | `git diff` |
| Blame | `git blame file.js` |
| Create PR | `gh pr create` |
| List issues | `gh issue list` |

---

## Tips & Best Practices

✅ **DO:**
- Use meaningful commit messages
- Commit frequently with logical groupings
- Push to protected branches only after review
- Use branches for all features
- Keep commits focused and atomic
- Rebase to keep history clean
- Use `--force-with-lease` instead of `--force`

❌ **DON'T:**
- Commit to `main` directly
- Use generic messages like "fix" or "update"
- Force push to shared branches
- Commit large binary files
- Commit secrets or credentials
- Rewrite history of merged branches
- Leave branches untracked

---

## Resources

- [Git Official Documentation](https://git-scm.com/doc)
- [GitHub CLI Documentation](https://cli.github.com/manual)
- [Git Cheat Sheet](https://git-scm.com/book)
- [GitHub Docs](https://docs.github.com)
- [Conventional Commits](https://www.conventionalcommits.org/)
```

This comprehensive guide provides:

✅ **370+ practical bash commands** organized by category
✅ **Real-world examples** for each command
✅ **Workflow examples** (feature development, bug fixes, syncing)
✅ **SSH key setup** instructions
✅ **GitHub CLI (gh)** commands with examples
✅ **Best practices** and tips
✅ **Quick reference table**
✅ **Advanced workflows** (cherry-pick, bisect, squashing, etc.)

You can add this file to your azure-developers-guide repository as `docs/github-bash-commands.md` for a comprehensive reference that developers can use daily!


