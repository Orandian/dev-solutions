# Industry Git Commands for Project Management

A comprehensive guide to Git commands essential for professional software development and project management in enterprise environments.

## Table of Contents
1. [Repository Setup & Initialization](#repository-setup--initialization)
2. [Branch Management Strategies](#branch-management-strategies)
3. [Collaborative Development](#collaborative-development)
4. [Code Review & Pull Requests](#code-review--pull-requests)
5. [Release Management](#release-management)
6. [Hotfix & Emergency Procedures](#hotfix--emergency-procedures)
7. [Advanced Git Operations](#advanced-git-operations)
8. [Git Flow Workflows](#git-flow-workflows)
9. [Troubleshooting & Recovery](#troubleshooting--recovery)
10. [Enterprise Best Practices](#enterprise-best-practices)

---

## Repository Setup & Initialization

### Initial Repository Setup
```bash
# Initialize new repository
git init

# Clone existing repository
git clone https://github.com/company/project.git
git clone git@github.com:company/project.git  # SSH

# Clone specific branch
git clone -b develop https://github.com/company/project.git

# Clone with custom directory name
git clone https://github.com/company/project.git my-project

# Set up remote repositories
git remote add origin https://github.com/company/project.git
git remote add upstream https://github.com/original/project.git
```

### Initial Configuration
```bash
# Configure user information
git config --global user.name "John Doe"
git config --global user.email "john.doe@company.com"

# Set default branch name
git config --global init.defaultBranch main

# Set up SSH key (for secure access)
ssh-keygen -t ed25519 -C "john.doe@company.com"
ssh-add ~/.ssh/id_ed25519

# Configure line endings (important for cross-platform teams)
git config --global core.autocrlf true  # Windows
git config --global core.autocrlf input # Mac/Linux

# Set default editor
git config --global core.editor "code --wait"  # VS Code
```

---

## Branch Management Strategies

### Creating and Managing Feature Branches
```bash
# Create and switch to new feature branch
git checkout -b feature/user-authentication
git switch -c feature/user-authentication  # Modern syntax

# Create branch from specific commit
git checkout -b hotfix/security-patch main

# List all branches (local and remote)
git branch -a
git branch -r  # Remote only

# Switch between branches
git checkout develop
git switch develop  # Modern syntax

# Rename branch
git branch -m old-name new-name
git branch -m new-name  # Rename current branch

# Delete branch (safe - prevents deletion if not merged)
git branch -d feature/completed-feature

# Force delete branch
git branch -D feature/abandoned-feature

# Delete remote branch
git push origin --delete feature/old-feature
```

### Branch Tracking and Updates
```bash
# Set upstream branch for current branch
git push -u origin feature/new-feature

# Track remote branch
git checkout --track origin/feature/existing-feature

# Update local branch with remote changes
git pull origin develop
git pull --rebase origin develop  # Rebase instead of merge

# Fetch all remote changes without merging
git fetch --all

# Show branch relationships
git branch -vv
```

---

## Collaborative Development

### Daily Development Workflow
```bash
# Start work day - update local repository
git fetch --all
git pull origin develop

# Create feature branch for new work
git checkout -b feature/payment-integration

# Stage and commit changes
git add .
git add -p  # Interactive staging (recommended)
git commit -m "feat: implement stripe payment integration"

# Push feature branch to remote
git push -u origin feature/payment-integration

# Regular commits during development
git add .
git commit -m "feat: add payment validation logic"
git push origin feature/payment-integration
```

### Working with Team Changes
```bash
# Update feature branch with latest develop
git checkout develop
git pull origin develop
git checkout feature/payment-integration
git merge develop

# Alternative: Rebase feature branch
git checkout feature/payment-integration
git rebase develop

# Interactive rebase for cleaning up commits
git rebase -i HEAD~3  # Last 3 commits

# Resolve merge conflicts
git status
# Edit conflicted files
git add resolved-file.js
git commit  # Complete merge
```

### Stashing Work in Progress
```bash
# Stash uncommitted changes
git stash
git stash save "work in progress on login feature"

# List stashes
git stash list

# Apply most recent stash
git stash pop
git stash apply  # Apply without removing from stash

# Apply specific stash
git stash apply stash@{2}

# Create branch from stash
git stash branch feature/from-stash
```

---

## Code Review & Pull Requests

### Preparing Code for Review
```bash
# Ensure branch is up to date
git checkout develop
git pull origin develop
git checkout feature/payment-integration
git rebase develop

# Clean up commit history before PR
git rebase -i HEAD~5  # Interactive rebase last 5 commits

# Push cleaned branch
git push --force-with-lease origin feature/payment-integration

# Create tag for release candidate
git tag -a v1.2.0-rc1 -m "Release candidate 1.2.0"
git push origin v1.2.0-rc1
```

### Code Review Commands
```bash
# Checkout PR for local testing
git fetch origin pull/123/head:pr-123
git checkout pr-123

# Compare branches
git diff develop..feature/payment-integration
git diff --name-only develop..feature/payment-integration

# Show commits in feature branch not in develop
git log develop..feature/payment-integration --oneline

# Check which branches contain specific commit
git branch --contains commit-hash
```

---

## Release Management

### Creating Releases
```bash
# Create release branch
git checkout -b release/v1.2.0 develop

# Bump version files, update changelog
git add .
git commit -m "chore: bump version to 1.2.0"

# Merge release to main
git checkout main
git merge --no-ff release/v1.2.0

# Tag the release
git tag -a v1.2.0 -m "Release version 1.2.0"

# Merge back to develop
git checkout develop
git merge --no-ff release/v1.2.0

# Push everything
git push origin main develop v1.2.0

# Delete release branch
git branch -d release/v1.2.0
git push origin --delete release/v1.2.0
```

### Managing Tags and Versions
```bash
# List all tags
git tag
git tag -l "v1.*"  # Filter tags

# Show tag information
git show v1.2.0

# Create signed tag
git tag -s v1.2.0 -m "Signed release 1.2.0"

# Push specific tag
git push origin v1.2.0

# Push all tags
git push origin --tags

# Delete tag locally and remotely
git tag -d v1.2.0
git push origin --delete v1.2.0

# Checkout specific version
git checkout v1.2.0
```

---

## Hotfix & Emergency Procedures

### Emergency Hotfix Process
```bash
# Create hotfix branch from main
git checkout main
git pull origin main
git checkout -b hotfix/security-vulnerability

# Make critical fix
git add fixed-file.js
git commit -m "fix: resolve critical security vulnerability"

# Test the fix
# Run automated tests, security scans

# Merge hotfix to main
git checkout main
git merge --no-ff hotfix/security-vulnerability

# Tag hotfix release
git tag -a v1.2.1 -m "Hotfix release 1.2.1"

# Merge hotfix to develop
git checkout develop
git merge --no-ff hotfix/security-vulnerability

# Push everything
git push origin main develop v1.2.1

# Clean up
git branch -d hotfix/security-vulnerability
git push origin --delete hotfix/security-vulnerability
```

### Reverting Changes in Production
```bash
# Revert specific commit
git revert commit-hash
git revert HEAD  # Revert last commit

# Revert merge commit
git revert -m 1 merge-commit-hash

# Revert multiple commits
git revert HEAD~3..HEAD

# Create revert PR instead of direct revert
git revert --no-commit HEAD~3..HEAD
git commit -m "Revert problematic changes from release 1.2.0"
```

---

## Advanced Git Operations

### Rebasing and History Management
```bash
# Interactive rebase to clean up history
git rebase -i HEAD~5

# Rebase feature branch onto develop
git rebase develop feature/payment-integration

# Continue rebase after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort

# Squash commits
git rebase -i HEAD~3
# Change 'pick' to 'squash' for commits to combine

# Split a commit
git rebase -i HEAD~2
# Change 'pick' to 'edit' for commit to split
git reset HEAD~
# Stage and commit files separately
git add file1.js
git commit -m "first part of original commit"
git add file2.js
git commit -m "second part of original commit"
git rebase --continue
```

### Cherry-picking and Selective Merging
```bash
# Cherry-pick specific commit
git cherry-pick commit-hash

# Cherry-pick without committing
git cherry-pick --no-commit commit-hash

# Cherry-pick range of commits
git cherry-pick commit1..commit2

# Cherry-pick from another branch
git cherry-pick feature/other-branch~3
```

### Advanced Merging Strategies
```bash
# Merge with no fast-forward (creates merge commit)
git merge --no-ff feature/payment-integration

# Merge and squash all commits
git merge --squash feature/payment-integration

# Merge with specific strategy
git merge -X ours feature/conflicting-branch  # Prefer our changes
git merge -X theirs feature/conflicting-branch  # Prefer their changes

# Abort merge
git merge --abort

# Continue merge after resolving conflicts
git commit  # Complete the merge
```

---

## Git Flow Workflows

### Git Flow Commands (using git-flow extension)
```bash
# Initialize git flow
git flow init

# Start new feature
git flow feature start payment-integration

# Finish feature (merges to develop)
git flow feature finish payment-integration

# Start release
git flow release start 1.2.0

# Finish release (merges to main and develop, creates tag)
git flow release finish 1.2.0

# Start hotfix
git flow hotfix start security-patch

# Finish hotfix
git flow hotfix finish security-patch
```

### GitHub Flow (Simplified)
```bash
# Create feature branch
git checkout -b feature/new-feature

# Work and commit
git add .
git commit -m "implement new feature"
git push origin feature/new-feature

# Create pull request (via GitHub UI)
# After review and approval, merge via GitHub

# Clean up local branch
git checkout main
git pull origin main
git branch -d feature/new-feature
```

---

## Troubleshooting & Recovery

### Undoing Changes
```bash
# Undo last commit (keep changes staged)
git reset --soft HEAD~1

# Undo last commit (unstage changes)
git reset HEAD~1
git reset --mixed HEAD~1  # Same as above

# Undo last commit (lose changes)
git reset --hard HEAD~1

# Undo changes to specific file
git checkout HEAD -- file.js
git restore file.js  # Modern syntax

# Unstage file
git reset HEAD file.js
git restore --staged file.js  # Modern syntax
```

### Recovery Operations
```bash
# Find lost commits
git reflog
git log --oneline --all

# Recover deleted branch
git checkout -b recovered-branch commit-hash

# Find commit that introduced bug
git bisect start
git bisect bad HEAD
git bisect good v1.1.0
# Git will checkout commits to test
git bisect good/bad  # Mark each commit
git bisect reset  # End bisect session

# Recover file from specific commit
git show commit-hash:path/to/file.js > recovered-file.js

# Find commits that changed specific file
git log --follow -- file.js
```

### Cleaning Repository
```bash
# Remove untracked files
git clean -n  # Dry run
git clean -f  # Remove files
git clean -fd  # Remove files and directories

# Remove ignored files
git clean -fX

# Garbage collect
git gc --aggressive --prune=now

# Verify repository integrity
git fsck
```

---

## Enterprise Best Practices

### Commit Message Standards
```bash
# Conventional Commits format
git commit -m "feat: add user authentication system"
git commit -m "fix: resolve login validation bug"
git commit -m "docs: update API documentation"
git commit -m "chore: update dependencies"
git commit -m "test: add unit tests for payment service"

# Multi-line commit messages
git commit -m "feat: implement payment processing

- Add Stripe integration
- Handle payment validation
- Add error handling for failed payments
- Update user interface for payment flow"
```

### Branch Protection and Policies
```bash
# Check if branch is protected (GitHub CLI)
gh api repos/:owner/:repo/branches/main

# Enforce signed commits
git config --global commit.gpgsign true

# Set up commit template
git config --global commit.template ~/.gitmessage
```

### Repository Maintenance
```bash
# Update all remote references
git remote update --prune

# Show repository statistics
git count-objects -v

# Find large files in repository
git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | awk '/^blob/ {print substr($0,6)}' | sort --numeric-sort --key=2

# Remove file from entire history (use carefully!)
git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch large-file.zip' --prune-empty --tag-name-filter cat -- --all
```

### Security and Access Management
```bash
# Sign commits with GPG
git commit -S -m "signed commit message"

# Verify signatures
git log --show-signature

# Configure GPG key
git config --global user.signingkey GPG-KEY-ID

# Use SSH for authentication
git config --global url."git@github.com:".insteadOf "https://github.com/"
```

---

## Daily Workflow Checklist

### Morning Routine
```bash
# 1. Update local repository
git fetch --all
git checkout develop
git pull origin develop

# 2. Check status of current work
git status
git stash list

# 3. Review what happened overnight
git log --oneline --since="1 day ago"
```

### Before Leaving Work
```bash
# 1. Commit or stash work in progress
git add .
git commit -m "wip: progress on feature X" || git stash

# 2. Push current branch
git push origin current-branch-name

# 3. Clean up workspace
git status  # Ensure clean working directory
```

### Weekly Maintenance
```bash
# 1. Update all branches
git fetch --all --prune

# 2. Clean up merged branches
git branch --merged | grep -v "\*\|main\|develop" | xargs -n 1 git branch -d

# 3. Update main and develop
git checkout main && git pull origin main
git checkout develop && git pull origin develop
```

---

## Emergency Commands Reference

| Situation | Command |
|-----------|---------|
| Need to switch branches quickly | `git stash && git checkout branch-name` |
| Accidentally committed to wrong branch | `git reset --soft HEAD~1` |
| Need to undo last commit but keep changes | `git reset --soft HEAD~1` |
| Made mistake in commit message | `git commit --amend -m "corrected message"` |
| Need to find when bug was introduced | `git bisect start && git bisect bad HEAD && git bisect good v1.0.0` |
| Accidentally deleted branch | `git reflog` then `git checkout -b branch-name commit-hash` |
| Production is broken, need quick revert | `git revert commit-hash && git push origin main` |
| Merge conflict during important release | `git merge --abort` then resolve properly |

Remember: Always backup important work, communicate with your team before force-pushing, and follow your company's specific Git workflows and policies!