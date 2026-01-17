---
title: "Git Advanced Commands - Power User Guide"
author: "Data Engineering Team"
date: "2026-01-17"
geometry: margin=2cm
fontsize: 10pt
---

\newpage

# Git Advanced Commands - Power User Guide

**Version:** 1.0.0  
**Last Updated:** January 17, 2026

---

## ⚠️ Important Warning

This guide contains **powerful Git commands** that can permanently alter repository history. Always:

- 🔴 **Create backups** before destructive operations
- 🔴 **Never rewrite public/shared history**
- 🔴 **Communicate with team** before force-pushing
- 🟡 **Test commands** on feature branches first
- 🟢 **Use reflog** as your safety net

---

## Table of Contents

1. Interactive Rebase
2. Cherry-Pick
3. Stash Advanced Usage
4. Submodules
5. Subtrees
6. Reflog (Recovery)
7. Bisect (Bug Hunting)
8. Filter-Branch & Filter-Repo
9. Worktrees
10. Hooks
11. Patch Management
12. Advanced Merging Strategies
13. Advanced Rebasing
14. Advanced Log & History
15. Sparse Checkout
16. Git Attributes
17. Advanced Configuration
18. Performance Optimization
19. Plumbing Commands
20. Advanced Troubleshooting
21. Command Reference Table
22. How to Export to PDF

\newpage

## 1. Interactive Rebase 🟡

Interactive rebase allows you to rewrite commit history with surgical precision.

### Complete Workflow

```bash
# Start interactive rebase for last 5 commits
git rebase -i HEAD~5

# Rebase onto specific branch
git rebase -i main

# Rebase from specific commit
git rebase -i abc123^
```

### All Commands Available

```
pick   abc123  Use commit as-is
reword def456  Change commit message
edit   ghi789  Pause to amend commit
squash jkl012  Combine with previous, edit message
fixup  mno345  Combine with previous, discard message
drop   pqr678  Remove commit entirely
```

### Visual Example: Before

```
A --- B --- C --- D --- E (main)
      ↑
    Bad commit message
      Need to fix
```

### After Interactive Rebase

```
A --- B' --- D' --- E' (main)
      ↑
    Fixed message, C removed
```

### Common Use Cases

```bash
# Squash last 3 commits into one
git rebase -i HEAD~3
# Change first 3 lines to: pick, squash, squash

# Split a commit
git rebase -i HEAD~1
# Change 'pick' to 'edit'
git reset HEAD^
git add file1 && git commit -m "Part 1"
git add file2 && git commit -m "Part 2"
git rebase --continue

# Reorder commits
# Simply rearrange lines in the interactive editor

# Abort rebase if something goes wrong
git rebase --abort
```

\newpage

## 2. Cherry-Pick 🟢

Apply specific commits from one branch to another.

### Visual Representation

```
main:     A --- B --- C --- D
                       
feature:  E --- F --- G --- H
               ↓ (cherry-pick F)
main:     A --- B --- C --- D --- F'
```

### Commands

```bash
# Cherry-pick single commit
git cherry-pick abc123

# Cherry-pick multiple commits
git cherry-pick abc123 def456 ghi789

# Cherry-pick range (exclusive start)
git cherry-pick abc123..def456

# Cherry-pick with new commit message
git cherry-pick abc123 --edit

# Cherry-pick without committing (stage only)
git cherry-pick abc123 --no-commit

# Continue after resolving conflicts
git cherry-pick --continue

# Abort cherry-pick
git cherry-pick --abort
```

### Options

```bash
-x              # Add "cherry picked from" note
-n, --no-commit # Don't commit, just stage
-e, --edit      # Edit commit message
--ff            # Fast-forward if possible
-m parent-num   # Cherry-pick merge commit (specify parent)
```

### Use Cases

- Hotfix to production from development branch
- Backport bug fixes to older releases
- Apply specific features to different branches
- Extract commits from abandoned branches

\newpage

## 3. Stash Advanced Usage 🟢

Temporarily shelve changes without committing.

### Stack Visualization

```
stash@{0}  ← Most recent (top)
stash@{1}
stash@{2}
stash@{3}  ← Oldest (bottom)
```

### All Stash Commands

```bash
# Save with description
git stash save "WIP: feature X"
git stash push -m "WIP: feature X"

# Stash including untracked files
git stash -u
git stash --include-untracked

# Stash all (including ignored)
git stash -a
git stash --all

# List all stashes
git stash list

# Show stash contents
git stash show
git stash show -p stash@{1}

# Apply stash (keep in stack)
git stash apply
git stash apply stash@{2}

# Pop stash (apply and remove)
git stash pop
git stash pop stash@{1}

# Drop specific stash
git stash drop stash@{0}

# Clear all stashes
git stash clear

# Create branch from stash
git stash branch new-feature stash@{0}
```

### Partial Stashing

```bash
# Interactive stashing
git stash -p

# Stash only staged changes
git stash --staged

# Stash specific files
git stash push -m "Partial work" path/to/file.txt

# Stash everything except specific files
git stash push -m "All but config" -- . ':!config.json'
```

\newpage

## 4. Submodules 🟡

Embed external repositories within your repository.

### Repository Structure Diagram

```
main-project/
├── .git/
├── .gitmodules          # Submodule configuration
├── src/
├── lib/
│   └── external-lib/    # ← Submodule (separate repo)
│       ├── .git/        # Points to external repo
│       └── ...
└── README.md
```

### Adding Submodules

```bash
# Add submodule
git submodule add https://github.com/user/repo.git path/to/submodule

# Add submodule on specific branch
git submodule add -b main https://github.com/user/repo.git lib/external

# Commit the submodule addition
git commit -m "Add external library as submodule"
```

### Cloning Projects with Submodules

```bash
# Clone and initialize submodules
git clone --recurse-submodules https://github.com/user/main-project.git

# Or clone then initialize
git clone https://github.com/user/main-project.git
cd main-project
git submodule init
git submodule update

# Or one-liner
git submodule update --init --recursive
```

### Updating Submodules

```bash
# Update specific submodule to latest
cd path/to/submodule
git pull origin main
cd ../..
git add path/to/submodule
git commit -m "Update submodule to latest"

# Update all submodules to remote latest
git submodule update --remote

# Update to commit recorded in superproject
git submodule update

# Parallel update (faster)
git submodule update --jobs 8
```

### Useful Submodule Commands

```bash
# Show submodule status
git submodule status

# Execute command in each submodule
git submodule foreach 'git pull origin main'

# Remove submodule
git submodule deinit path/to/submodule
git rm path/to/submodule
rm -rf .git/modules/path/to/submodule
```

\newpage

## 5. Subtrees 🟡

Alternative to submodules - copy external repo into your repo.

### Comparison: Submodules vs Subtrees

| Feature | Submodules | Subtrees |
|---------|-----------|----------|
| Complexity | Higher | Lower |
| Separate .git | Yes | No |
| Clone simplicity | Requires --recurse | Works normally |
| History | Separate | Merged |
| Updates | Manual | Manual |
| Best for | Libraries | Vendor code |
| Team learning curve | Steeper | Gentler |

### Adding Subtree

```bash
# Add remote
git remote add lib-remote https://github.com/user/library.git

# Add subtree (pull method)
git subtree add --prefix=lib/external lib-remote main --squash

# Or direct URL
git subtree add --prefix=vendor/lib https://github.com/user/lib.git main --squash
```

### Updating Subtree

```bash
# Pull updates
git subtree pull --prefix=lib/external lib-remote main --squash

# Or with URL
git subtree pull --prefix=vendor/lib https://github.com/user/lib.git main --squash
```

### Pushing Changes Back

```bash
# Push changes to upstream
git subtree push --prefix=lib/external lib-remote main

# Split out subtree history
git subtree split --prefix=lib/external --branch lib-only
```

### When to Use

**Use Subtrees when:**
- Simple integration needed
- Rarely update external code
- Want unified history
- Team unfamiliar with submodules

**Use Submodules when:**
- Need specific versions
- Active external development
- Multiple projects share dependency
- Want clear separation

\newpage

## 6. Reflog (Recovery) 🟢

Your safety net - track all HEAD movements.

### Reflog Visualization

```
HEAD@{0}  Reset to previous commit      (2 minutes ago)
HEAD@{1}  Commit: Add feature           (10 minutes ago)
HEAD@{2}  Checkout: main                (1 hour ago)
HEAD@{3}  Commit: Fix bug               (2 hours ago)
HEAD@{4}  Merge: feature-branch         (1 day ago)
HEAD@{5}  Reset --hard HEAD~3           (1 day ago)  ← Accidental!
HEAD@{6}  Commit: Important work        (1 day ago)  ← Lost commit!
```

### Recovery Commands

```bash
# View reflog
git reflog
git reflog show HEAD

# View reflog for specific branch
git reflog show main

# Reflog with dates
git reflog --date=iso

# Recover lost commit
git checkout HEAD@{6}
git branch recovery-branch

# Or cherry-pick
git cherry-pick HEAD@{6}

# Undo bad reset
git reset --hard HEAD@{5}

# Find lost commits
git fsck --lost-found
```

### Recovery Scenarios

```bash
# Scenario 1: Accidental hard reset
git reset --hard HEAD~3  # Oops!
git reflog               # Find previous HEAD
git reset --hard HEAD@{1}

# Scenario 2: Deleted branch
git branch -D feature    # Deleted!
git reflog               # Find last commit
git branch feature HEAD@{2}

# Scenario 3: Amended commit by mistake
git commit --amend       # Lost original!
git reset --soft HEAD@{1}

# Scenario 4: Bad rebase
git rebase main          # Conflicts/mess
git rebase --abort       # Or
git reset --hard HEAD@{1}
```

### Time-Travel Diagram

```
      HEAD@{4}      HEAD@{2}      HEAD@{0}
         ↓             ↓             ↓
A --- B --- C --- D --- E --- F --- G (main)
               ↑
           HEAD@{6} (lost commit recovered)
```

\newpage

## 7. Bisect (Bug Hunting) 🟢

Binary search to find the commit that introduced a bug.

### Binary Search Visualization

```
Total commits: 16
Step 1: Test commit 8  → Bad   (eliminate 8-16)
Step 2: Test commit 4  → Good  (eliminate 1-4)
Step 3: Test commit 6  → Bad   (eliminate 6-8)
Step 4: Test commit 5  → Good  (found: commit 6 is culprit!)

Total steps: 4 (vs. 16 linear checks)
```

### Complete Workflow

```bash
# Start bisect
git bisect start

# Mark current commit as bad
git bisect bad

# Mark known good commit
git bisect good v1.2.0

# Git checks out middle commit - test it
npm test

# If test fails
git bisect bad

# If test passes
git bisect good

# Continue until found
# Git will say: "abc123 is the first bad commit"

# View bisect log
git bisect log

# End bisect
git bisect reset
```

### Automated Bisect

```bash
# Bisect with automated script
git bisect start HEAD v1.0.0
git bisect run npm test

# Bisect with custom test script
git bisect start
git bisect bad
git bisect good abc123
git bisect run ./test-script.sh

# Example test-script.sh
#!/bin/bash
make || exit 125  # Skip if doesn't build
./run-tests
```

### Bisect Options

```bash
# Skip untestable commit
git bisect skip

# Mark multiple commits
git bisect good abc123 def456
git bisect bad ghi789 jkl012

# Bisect on specific path
git bisect start -- path/to/file.js

# Visualize bisect
git bisect visualize
git bisect view
```

\newpage

## 8. Filter-Branch & Filter-Repo 🔴

Rewrite repository history - extremely powerful and dangerous.

### ⚠️ Critical Warnings

- 🔴 **CREATES NEW HISTORY** - All commit SHAs change
- 🔴 **REQUIRES FORCE PUSH** - Breaks all forks
- 🔴 **BACKUP FIRST** - No easy undo
- 🔴 **COORDINATE WITH TEAM** - Everyone must re-clone
- 🟡 **Use filter-repo** over filter-branch (faster, safer)

### Filter-Branch (Legacy)

```bash
# Remove file from all history
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD

# Remove file (faster with index-filter)
git filter-branch --index-filter \
  'git rm --cached --ignore-unmatch secrets.env' HEAD

# Change author email
git filter-branch --env-filter '
if [ "$GIT_AUTHOR_EMAIL" = "old@email.com" ]; then
  export GIT_AUTHOR_EMAIL="new@email.com"
fi' HEAD

# Change committer
git filter-branch --commit-filter '
if [ "$GIT_AUTHOR_NAME" = "Old Name" ]; then
  GIT_AUTHOR_NAME="New Name"
  GIT_COMMITTER_NAME="New Name"
  git commit-tree "$@"
else
  git commit-tree "$@"
fi' HEAD
```

### Git Filter-Repo (Recommended)

```bash
# Install
pip install git-filter-repo

# Remove file
git filter-repo --path passwords.txt --invert-paths

# Remove folder
git filter-repo --path secret-folder/ --invert-paths

# Keep only specific paths
git filter-repo --path src/ --path docs/

# Replace text
echo "password123==>\bREDACTED" > replacements.txt
git filter-repo --replace-text replacements.txt

# Change author
git filter-repo --mailmap mailmap.txt

# Analyze repository
git filter-repo --analyze
```

### BFG Repo-Cleaner (Alternative)

```bash
# Install BFG
brew install bfg  # or download jar

# Remove large files
bfg --strip-blobs-bigger-than 10M repo.git

# Remove specific file
bfg --delete-files passwords.txt repo.git

# Replace passwords
echo "password123" > passwords.txt
bfg --replace-text passwords.txt repo.git

# Clean up
cd repo.git
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```

### Use Cases

- Remove sensitive data (passwords, keys)
- Remove large binary files
- Split monorepo into separate repos
- Change author information
- Clean up messy history before open-sourcing

\newpage

## 9. Worktrees 🟢

Multiple working directories from a single repository.

### File System Visualization

```
~/projects/
  myrepo/.git/          # Main repository
  myrepo/               # Main worktree (main branch)
  │   ├── src/
  │   └── README.md
  │
  myrepo-feature/       # Worktree (feature branch)
  │   ├── src/
  │   └── README.md
  │
  myrepo-hotfix/        # Worktree (hotfix branch)
      ├── src/
      └── README.md

All share same .git database!
```

### Commands

```bash
# Create new worktree
git worktree add ../myrepo-feature feature-branch

# Create worktree with new branch
git worktree add -b new-feature ../myrepo-feature main

# Create temporary worktree for commit
git worktree add --detach ../myrepo-temp abc123

# List all worktrees
git worktree list

# Detailed listing
git worktree list --porcelain

# Remove worktree
git worktree remove ../myrepo-feature

# Prune stale worktree references
git worktree prune

# Move worktree
git worktree move myrepo-feature new-location
```

### Use Cases

**Parallel Development:**
```bash
# Work on feature while running tests on main
git worktree add ../myrepo-feature feature-x
cd ../myrepo-feature
# Develop here

cd ../myrepo
npm test  # Test main branch simultaneously
```

**Emergency Hotfixes:**
```bash
# Don't want to stash current work
git worktree add -b hotfix ../myrepo-hotfix main
cd ../myrepo-hotfix
# Fix, commit, push
cd ../myrepo
# Continue previous work
```

**Code Review:**
```bash
# Review PR without switching branches
git worktree add ../review-pr pr-branch
cd ../review-pr
# Review code
cd ..
git worktree remove review-pr
```

**Testing Different Versions:**
```bash
git worktree add ../test-v1 v1.0.0
git worktree add ../test-v2 v2.0.0
# Compare behavior
```

\newpage

## 10. Hooks 🟡

Automate workflows with Git lifecycle scripts.

### Lifecycle Diagram

```
LOCAL HOOKS:
  ┌─────────────────────┐
  │   pre-commit        │ → Lint, format, test
  └─────────────────────┘
           ↓
  ┌─────────────────────┐
  │   prepare-commit-msg│ → Auto-generate message
  └─────────────────────┘
           ↓
  ┌─────────────────────┐
  │   commit-msg        │ → Validate message format
  └─────────────────────┘
           ↓
  ┌─────────────────────┐
  │   post-commit       │ → Notify, backup
  └─────────────────────┘
           ↓
  ┌─────────────────────┐
  │   pre-push          │ → Run tests before push
  └─────────────────────┘

SERVER HOOKS:
  ┌─────────────────────┐
  │   pre-receive       │ → Validate incoming push
  └─────────────────────┘
           ↓
  ┌─────────────────────┐
  │   update            │ → Enforce branch policies
  └─────────────────────┘
           ↓
  ┌─────────────────────┐
  │   post-receive      │ → Deploy, notify team
  └─────────────────────┘
```

### Available Hooks

**Client-Side:**
- `pre-commit` - Before commit creation
- `prepare-commit-msg` - Before commit message editor
- `commit-msg` - After commit message entered
- `post-commit` - After commit created
- `pre-rebase` - Before rebase
- `post-rewrite` - After commands that rewrite commits
- `post-checkout` - After checkout
- `post-merge` - After merge
- `pre-push` - Before push
- `pre-auto-gc` - Before garbage collection

**Server-Side:**
- `pre-receive` - Before refs updated
- `update` - For each branch being updated
- `post-receive` - After refs updated
- `post-update` - After all refs updated

### Example Scripts

**pre-commit (Linting):**
```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Running pre-commit checks..."

# Run linter
npm run lint
if [ $? -ne 0 ]; then
  echo "❌ Linting failed. Commit aborted."
  exit 1
fi

# Run tests
npm test
if [ $? -ne 0 ]; then
  echo "❌ Tests failed. Commit aborted."
  exit 1
fi

echo "✅ All checks passed!"
exit 0
```

**commit-msg (Message Validation):**
```bash
#!/bin/bash
# .git/hooks/commit-msg

commit_msg=$(cat "$1")

# Check for Jira ticket
if ! echo "$commit_msg" | grep -qE "^[A-Z]+-[0-9]+:"; then
  echo "❌ Commit message must start with Jira ticket (e.g., PROJ-123: message)"
  exit 1
fi

# Check minimum length
if [ ${#commit_msg} -lt 10 ]; then
  echo "❌ Commit message too short (min 10 chars)"
  exit 1
fi

exit 0
```

**pre-push (Test Before Push):**
```bash
#!/bin/bash
# .git/hooks/pre-push

echo "Running tests before push..."

npm run test:all
if [ $? -ne 0 ]; then
  echo "❌ Tests failed. Push aborted."
  echo "Fix tests or use --no-verify to skip."
  exit 1
fi

exit 0
```

**post-receive (Auto Deploy):**
```bash
#!/bin/bash
# Server: .git/hooks/post-receive

while read oldrev newrev ref
do
  if [[ $ref =~ refs/heads/main ]]; then
    echo "Deploying main branch..."
    cd /var/www/app
    git pull origin main
    npm install
    npm run build
    sudo systemctl restart app
    echo "✅ Deployment complete!"
  fi
done
```

### Managing Hooks

```bash
# Hooks location
.git/hooks/

# Make hook executable
chmod +x .git/hooks/pre-commit

# Bypass hooks (use sparingly)
git commit --no-verify
git push --no-verify

# Share hooks with team (not in .git/hooks)
mkdir .githooks
# Add hooks here
git config core.hooksPath .githooks
```

\newpage

## 11. Patch Management 🟢

Share changes via email or files without pushing to remote.

### Workflow Diagram

```
Developer A:
  ┌──────────────────┐
  │ Create commits   │
  └──────────────────┘
          ↓
  ┌──────────────────┐
  │ git format-patch │
  └──────────────────┘
          ↓
  ┌──────────────────┐
  │ 0001-fix.patch   │ ───→ Email/File Transfer ───→
  │ 0002-feat.patch  │
  └──────────────────┘

Developer B:
                      ┌──────────────────┐
                      │ Receive patches  │
                      └──────────────────┘
                              ↓
                      ┌──────────────────┐
                      │ git am           │
                      └──────────────────┘
                              ↓
                      ┌──────────────────┐
                      │ Commits applied  │
                      └──────────────────┘
```

### Format-Patch

```bash
# Create patch for last commit
git format-patch -1

# Create patches for last 3 commits
git format-patch -3

# Patches since specific commit
git format-patch abc123

# Patches between commits
git format-patch abc123..def456

# Patch for specific branch
git format-patch main..feature-branch

# Output to specific directory
git format-patch -o patches/ -3

# Single patch file
git format-patch -1 --stdout > my-changes.patch

# Include cover letter
git format-patch -3 --cover-letter
```

### Apply Patches (git am)

```bash
# Apply single patch
git am 0001-fix-bug.patch

# Apply all patches in directory
git am patches/*.patch

# Apply patch with 3-way merge
git am -3 0001-fix.patch

# Apply patch interactively
git am -i patches/*.patch

# Apply and sign-off
git am --signoff 0001-fix.patch

# Resolve conflicts
git am --show-current-patch
# Fix conflicts
git add .
git am --continue

# Skip problematic patch
git am --skip

# Abort applying patches
git am --abort
```

### Alternative: Apply (for non-mailed patches)

```bash
# Apply patch without commit
git apply my-changes.patch

# Check if patch applies cleanly
git apply --check my-changes.patch

# Show stats
git apply --stat my-changes.patch

# Apply with 3-way merge
git apply -3 my-changes.patch

# Reverse a patch
git apply -R my-changes.patch
```

### Email Workflow

```bash
# Configure Git for email
git config --global sendemail.smtpserver smtp.gmail.com
git config --global sendemail.smtpuser you@gmail.com
git config --global sendemail.smtpencryption tls
git config --global sendemail.smtpserverport 587

# Send patches via email
git send-email --to=maintainer@project.org patches/*.patch

# Request pull (alternative to patches)
git request-pull v1.0 https://github.com/user/repo.git feature
```

\newpage

## 12. Advanced Merging Strategies 🟡

Different merge strategies for different scenarios.

### Strategy Comparison Table

| Strategy | Description | Use Case | Fast-Forward |
|----------|-------------|----------|--------------|
| **recursive** | Default 3-way merge | Most situations | If possible |
| **ours** | Keep our version | Discard their changes | No |
| **theirs** | Take their version | Accept all incoming | No |
| **octopus** | Merge 3+ branches | Release integration | Yes |
| **resolve** | Legacy 3-way | Simple conflicts only | If possible |
| **subtree** | Modified recursive | Subtree merges | If possible |

### Recursive Strategy (Default)

```bash
# Standard merge
git merge feature-branch

# Recursive with options
git merge -X ours feature-branch       # Prefer our changes
git merge -X theirs feature-branch     # Prefer their changes
git merge -X patience feature-branch   # Better diff algorithm
git merge -X ignore-space-change feature-branch
```

### Ours Strategy

```bash
# Keep our tree, record merge
git merge -s ours old-feature

# Use case: Mark branch as merged without applying changes
git merge -s ours deprecated-feature
# History shows merge, but no code changes
```

### Theirs Strategy (via -X)

```bash
# Accept all their changes (no pure 'theirs' strategy)
git merge -X theirs feature-branch

# Or manual theirs strategy
git merge --no-commit feature-branch
git checkout --theirs .
git commit
```

### Octopus Strategy

```bash
# Merge multiple branches at once
git merge feature-a feature-b feature-c

# Creates single merge commit with 4 parents
git merge -s octopus branch1 branch2 branch3

# Abort if conflicts (requires manual resolution)
```

### Subtree Strategy

```bash
# Merge from subtree
git merge -s subtree feature-branch

# Specify subtree prefix
git merge -X subtree=lib/ feature-branch
```

### Merge Options

```bash
# No fast-forward (always create merge commit)
git merge --no-ff feature-branch

# Fast-forward only (fail if not possible)
git merge --ff-only feature-branch

# Squash merge (combine all commits)
git merge --squash feature-branch
git commit -m "Merge feature: description"

# No commit (stage only)
git merge --no-commit feature-branch

# Abort merge
git merge --abort

# Custom merge message
git merge -m "Merge: Add authentication" feature-branch
```

\newpage

## 13. Advanced Rebasing 🟡

Complex rebase operations for sophisticated history management.

### Rebase --onto Visualization

**Scenario:** Move feature branch to different base

```
Before:
main:     A --- B --- C --- D
                ↓
old-base:       E --- F
                      ↓
feature:              G --- H --- I

Command:
git rebase --onto main old-base feature

After:
main:     A --- B --- C --- D
                            ↓
feature:                    G' --- H' --- I'

old-base:       E --- F (unchanged)
```

### Rebase --onto Commands

```bash
# Move feature from old-base to main
git rebase --onto main old-base feature

# Move last 3 commits to different branch
git rebase --onto main HEAD~3 HEAD

# Remove commits from middle of branch
git rebase --onto HEAD~5 HEAD~3

# Transplant range of commits
git rebase --onto new-base old-start old-end
```

### Complex Scenarios

**Scenario 1: Remove commits from middle**
```bash
# Remove commits D and E, keep F onwards
# A --- B --- C --- D --- E --- F --- G
git rebase --onto HEAD~3 HEAD~1
# Result: A --- B --- C --- F' --- G'
```

**Scenario 2: Split branch history**
```bash
# Keep only recent work from feature branch
git rebase --onto main feature~5 feature
```

**Scenario 3: Rebase with conflicts**
```bash
git rebase main
# Conflict occurs
git status                    # See conflicts
git diff                      # View conflict details
# Resolve conflicts in files
git add resolved-file.js
git rebase --continue         # Continue rebase

# Or skip commit
git rebase --skip

# Or abort
git rebase --abort
```

### Interactive Rebase Options

```bash
# Interactive rebase with autosquash
git commit --fixup abc123
git rebase -i --autosquash main

# Interactive with exec (run command after each commit)
git rebase -i --exec "npm test" HEAD~5

# Preserve merge commits
git rebase -i --rebase-merges main

# Update refs during rebase
git rebase -i --update-refs HEAD~5
```

### Rebase vs Merge Decision Tree

```
Need clean linear history?
├─ Yes → REBASE
└─ No
   ├─ Working alone? → REBASE
   └─ Working with team?
      ├─ Feature branch? → REBASE before merge
      └─ Shared branch? → MERGE (never rebase)
```

\newpage

## 14. Advanced Log & History 🟢

Powerful commands to search and analyze repository history.

### Custom Formats

```bash
# One-line format
git log --oneline

# Custom format
git log --pretty=format:"%h - %an, %ar : %s"

# Detailed custom format
git log --pretty=format:"%C(yellow)%h%C(reset) %C(blue)%ad%C(reset) %C(green)%an%C(reset) %s" --date=short

# Graph visualization
git log --graph --oneline --all

# Detailed graph
git log --graph --pretty=format:"%C(bold)%h%C(reset) %s %C(dim)- %an (%ar)"

# Date filtering
git log --since="2 weeks ago"
git log --after="2024-01-01" --before="2024-12-31"

# Author filtering
git log --author="John"

# Committer filtering
git log --committer="Jane"
```

### Searching Commits

**Search commit messages:**
```bash
# Search commit messages
git log --grep="fix"

# Case-insensitive search
git log --grep="fix" -i

# Multiple patterns (OR)
git log --grep="fix" --grep="bug"

# Multiple patterns (AND)
git log --grep="fix" --grep="bug" --all-match

# Invert match
git log --grep="WIP" --invert-grep
```

**Search code changes (-S pickaxe):**
```bash
# Find when "function authenticate" was added/removed
git log -S "function authenticate"

# Show patches
git log -S "function authenticate" -p

# Count occurrences change
git log -S "TODO" --oneline
```

**Search code changes (-G regex):**
```bash
# Find changes matching regex pattern
git log -G "function.*authenticate"

# Show patches
git log -G "TODO|FIXME" -p
```

**Search by file:**
```bash
# Show commits affecting file
git log -- path/to/file.js

# Show commits with patches
git log -p -- path/to/file.js

# Follow renames
git log --follow -- path/to/file.js

# Show commits affecting multiple files
git log -- file1.js file2.js

# All files in directory
git log -- src/components/
```

### Git Blame

```bash
# Show line-by-line attribution
git blame file.js

# Show line range
git blame -L 10,20 file.js

# Show in porcelain format (machine-readable)
git blame --porcelain file.js

# Show email instead of name
git blame -e file.js

# Follow file renames
git blame -C -C file.js

# Show commits from specific date
git blame --since=2024-01-01 file.js

# Ignore whitespace changes
git blame -w file.js

# Ignore revisions (like formatting commits)
git blame --ignore-rev abc123 file.js
git blame --ignore-revs-file .git-blame-ignore-revs file.js
```

### Stat and Shortlog

```bash
# Show file change statistics
git log --stat

# Compact stat
git log --shortstat

# Show authors and commit count
git shortlog -sn

# Show authors with email
git shortlog -sne

# Contribution summary
git shortlog --since="last month" --author="John"

# Line changes by author
git log --author="John" --pretty=tformat: --numstat | \
  awk '{add+=$1; del+=$2} END {print "added:", add, "deleted:", del}'
```

\newpage

## 15. Sparse Checkout 🟡

Work with subset of repository files.

### Use Cases

- Large monorepo (only need specific services)
- Slow network (reduce clone size)
- CI/CD optimization (build specific components)
- Security (limit file access)

### Workflow

```bash
# Clone repository without checkout
git clone --no-checkout https://github.com/user/monorepo.git
cd monorepo

# Enable sparse checkout
git sparse-checkout init

# Checkout only specific paths
git sparse-checkout set "src/service-a" "docs"

# Add more paths
git sparse-checkout add "src/shared"

# List current sparse paths
git sparse-checkout list

# Checkout everything
git sparse-checkout disable

# Use cone mode (recommended, faster)
git sparse-checkout init --cone
git sparse-checkout set src/service-a
```

### Cone vs Non-Cone Mode

```bash
# Cone mode (simpler, faster)
git sparse-checkout init --cone
git sparse-checkout set folder1 folder2

# Non-cone mode (full pattern matching)
git sparse-checkout init --no-cone
git sparse-checkout set "*.js" "!test/**"
```

### Patterns

```bash
# Edit sparse-checkout file directly
vim .git/info/sparse-checkout

# Example patterns:
/src/service-a/
/docs/
!/docs/internal/
*.md
!test/
```

### Example: Monorepo

```
monorepo/
├── services/
│   ├── api/
│   ├── web/
│   ├── worker/
├── shared/
└── docs/

# Only checkout api service and shared
git sparse-checkout set "services/api" "shared"
```

\newpage

## 16. Git Attributes 🟢

Configure per-file or per-path repository settings.

### .gitattributes Examples

**Line Ending Normalization:**
```
# .gitattributes

# Auto-detect text files and normalize
* text=auto

# Explicitly set line endings
*.sh text eol=lf
*.bat text eol=crlf
*.ps1 text eol=crlf

# Specific files
*.c text
*.h text
*.md text

# Binary files
*.png binary
*.jpg binary
*.pdf binary
*.exe binary
```

**Diff Drivers:**
```
# Custom diff for specific files
*.json diff=json
*.md diff=markdown

# Configure drivers in .git/config:
# [diff "json"]
#     textconv = jq .

# Don't diff binary files
*.zip -diff
*.jar -diff
```

**Merge Drivers:**
```
# Keep ours on conflict
database.lock merge=ours

# Custom merge driver
*.xml merge=xmlmerge
```

**Export Control:**
```
# Don't include in archive exports
.gitattributes export-ignore
.gitignore export-ignore
tests/ export-ignore
*.test.js export-ignore
```

**Filter (Clean/Smudge):**
```
# Run filter on checkout/commit
*.template filter=erb
*.secret filter=encrypt

# Configure in .git/config:
# [filter "encrypt"]
#     clean = openssl enc -aes-256-cbc -salt
#     smudge = openssl enc -aes-256-cbc -d
```

**Language Detection:**
```
# Override language detection on GitHub
*.js linguist-language=TypeScript
docs/* linguist-documentation
vendor/* linguist-vendored
```

### Common Patterns

```
# Comprehensive .gitattributes example

# Text files
* text=auto

# Scripts
*.sh text eol=lf
*.bash text eol=lf

# Windows files
*.bat text eol=crlf
*.cmd text eol=crlf
*.ps1 text eol=crlf

# Source code
*.c text diff=c
*.h text diff=c
*.cpp text diff=cpp
*.java text diff=java
*.py text diff=python
*.js text diff=javascript
*.ts text diff=typescript
*.json text

# Config files
*.yml text
*.yaml text
*.toml text
*.xml text
*.ini text

# Documentation
*.md text diff=markdown
*.txt text
*.adoc text

# Binary files
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
*.pdf binary
*.zip binary
*.tar.gz binary
*.exe binary
*.dll binary
*.so binary
*.dylib binary

# Font files
*.ttf binary
*.woff binary
*.woff2 binary

# Archives export-ignore
.gitattributes export-ignore
.gitignore export-ignore
.github/ export-ignore
tests/ export-ignore
docs/ export-ignore
*.test.* export-ignore
```

### Commands

```bash
# Check attributes for file
git check-attr -a path/to/file.txt

# Check specific attribute
git check-attr diff path/to/file.txt

# Re-normalize line endings
git add --renormalize .
git commit -m "Normalize line endings"
```

\newpage

## 17. Advanced Configuration 🟢

Powerful settings and aliases to supercharge Git.

### Powerful Aliases

```bash
# Add to ~/.gitconfig or run git config --global alias.name "command"

[alias]
    # Quick shortcuts
    co = checkout
    br = branch
    ci = commit
    st = status
    
    # Pretty logs
    lg = log --graph --pretty=format:'%C(bold)%h%C(reset) - %C(cyan)%an%C(reset): %s %C(yellow)(%cr)%C(reset)' --abbrev-commit --date=relative
    lga = log --graph --pretty=format:'%C(bold)%h%C(reset) - %C(cyan)%an%C(reset): %s %C(yellow)(%cr)%C(reset)' --abbrev-commit --date=relative --all
    tree = log --graph --oneline --decorate --all
    
    # Status shortcuts
    s = status -sb
    ss = status
    
    # Diff shortcuts
    d = diff
    dc = diff --cached
    ds = diff --stat
    
    # Commit shortcuts
    ca = commit --amend
    can = commit --amend --no-edit
    
    # Branch management
    brd = branch -d
    brD = branch -D
    bra = branch -a
    brr = branch -r
    
    # Undo operations
    undo = reset HEAD~1 --mixed
    unstage = reset HEAD --
    last = log -1 HEAD --stat
    
    # Find commits
    find = log --all --pretty=format:'%h - %s' --grep
    
    # Stash shortcuts
    sl = stash list
    sp = stash pop
    sa = stash apply
    ss = stash save
    
    # Advanced workflows
    wip = commit -am "WIP"
    unwip = reset HEAD~1
    
    # Cleanup
    cleanup = "!git branch --merged | grep -v '\\*\\|main\\|master\\|develop' | xargs -n 1 git branch -d"
    
    # Contributors
    contributors = shortlog -sn
    
    # Show aliases
    aliases = "!git config --get-regexp ^alias\\. | sed -e s/^alias\\.// -e s/\\ /\\ =\\ /"
```

### Conditional Configuration

```bash
# ~/.gitconfig

[user]
    name = Your Name
    email = personal@email.com

# Work configuration for work directory
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work

# Open source configuration
[includeIf "gitdir:~/oss/"]
    path = ~/.gitconfig-oss
```

```bash
# ~/.gitconfig-work
[user]
    email = work@company.com
    
[commit]
    gpgsign = true
```

### Custom Diff and Merge Tools

```bash
# Configure VS Code as diff tool
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'

# Configure VS Code as merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Configure Beyond Compare
git config --global diff.tool bc
git config --global difftool.bc.path "c:/Program Files/Beyond Compare 4/bcomp.exe"

# Configure P4Merge
git config --global merge.tool p4merge
git config --global mergetool.p4merge.path "/Applications/p4merge.app/Contents/MacOS/p4merge"
```

### Useful Configurations

```bash
# Automatically prune deleted branches
git config --global fetch.prune true

# Reuse recorded conflict resolutions
git config --global rerere.enabled true

# Better diff algorithm
git config --global diff.algorithm patience

# Show submodule changes in status
git config --global status.submoduleSummary true

# Default branch name
git config --global init.defaultBranch main

# GPG signing
git config --global commit.gpgsign true
git config --global user.signingkey YOUR_GPG_KEY_ID

# Color output
git config --global color.ui auto

# Default editor
git config --global core.editor "vim"

# Pager
git config --global core.pager "less -FRX"

# Line endings
git config --global core.autocrlf input  # Mac/Linux
git config --global core.autocrlf true   # Windows
```

\newpage

## 18. Performance Optimization 🟢

Keep your repository fast and efficient.

### Git Garbage Collection

```bash
# Manual garbage collection
git gc

# Aggressive garbage collection (slower but more thorough)
git gc --aggressive

# Garbage collection with prune
git gc --prune=now

# Auto garbage collection (runs automatically)
git gc --auto

# Check if gc needed
git count-objects -v
```

### Prune Operations

```bash
# Prune unreachable objects older than 2 weeks
git prune

# Prune immediately
git prune --expire=now

# Prune remote tracking branches
git remote prune origin

# Dry run
git remote prune origin --dry-run

# Prune reflog entries
git reflog expire --expire=30.days --all
git reflog expire --expire-unreachable=now --all
```

### File System Check

```bash
# Check repository integrity
git fsck

# Check with full details
git fsck --full

# Find dangling objects
git fsck --lost-found

# Unreachable objects
git fsck --unreachable
```

### Maintenance Commands

```bash
# Run all maintenance tasks
git maintenance run

# Start background maintenance
git maintenance start

# Stop background maintenance
git maintenance stop

# Register repository for maintenance
git maintenance register

# Show maintenance status
git config --get-all maintenance.repo
```

### Optimization Tips

**Large Files:**
```bash
# Find large files in history
git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  sed -n 's/^blob //p' | \
  sort --numeric-sort --key=2 | \
  tail -20

# Use Git LFS for large files
git lfs install
git lfs track "*.psd"
git lfs track "*.zip"
```

**Shallow Clones:**
```bash
# Clone with limited history (faster)
git clone --depth 1 https://github.com/user/repo.git

# Fetch more history later
git fetch --unshallow

# Clone specific branch only
git clone --single-branch --branch main https://github.com/user/repo.git
```

**Partial Clones:**
```bash
# Clone without blobs (fetch on demand)
git clone --filter=blob:none https://github.com/user/repo.git

# Clone without trees
git clone --filter=tree:0 https://github.com/user/repo.git
```

**Configuration:**
```bash
# Enable parallel fetching
git config --global fetch.parallel 8

# Enable commit graph
git config --global core.commitGraph true
git commit-graph write

# Enable multi-pack-index
git config --global core.multiPackIndex true
git multi-pack-index write

# Increase compression
git config --global pack.compression 9
```

### Regular Maintenance Schedule

```bash
# Weekly maintenance script
#!/bin/bash
echo "Running Git maintenance..."
git gc --auto
git remote prune origin
git reflog expire --expire=30.days --all
git maintenance run
echo "Maintenance complete!"
```

\newpage

## 19. Plumbing Commands 🟡

Low-level Git commands for advanced users.

### What Are Plumbing Commands?

Git has two types of commands:
- **Porcelain**: User-friendly commands (commit, push, pull)
- **Plumbing**: Low-level internal commands

### Commonly Useful Plumbing Commands

**Object Inspection:**
```bash
# Show object type
git cat-file -t abc123

# Show object size
git cat-file -s abc123

# Show object contents
git cat-file -p abc123

# Pretty-print commit/tree/blob
git show abc123
```

**Tree Operations:**
```bash
# Read tree into index
git read-tree abc123

# Write tree from index
git write-tree

# List tree contents
git ls-tree HEAD
git ls-tree -r HEAD  # Recursive
```

**References:**
```bash
# Update ref
git update-ref refs/heads/feature abc123

# Show ref
git show-ref

# Symbolic ref
git symbolic-ref HEAD
git symbolic-ref HEAD refs/heads/main
```

**Index Operations:**
```bash
# Show staged files
git ls-files --stage

# Update index
git update-index --add file.txt
git update-index --remove file.txt

# Hash object (without adding)
git hash-object file.txt

# Hash and add to database
git hash-object -w file.txt
```

**Commit Operations:**
```bash
# Create commit object
echo "Commit message" | git commit-tree abc123

# With parent
echo "Message" | git commit-tree abc123 -p def456
```

**Diff Operations:**
```bash
# Diff two trees
git diff-tree --no-commit-id --name-only -r abc123

# Diff index and working tree
git diff-files

# Diff index and HEAD
git diff-index HEAD
```

**Pack Operations:**
```bash
# Verify pack file
git verify-pack -v .git/objects/pack/pack-*.idx

# Index pack file
git index-pack pack-file.pack

# Create pack
git pack-objects --all < object-list
```

### Use Cases

**Scripting:**
```bash
# Get current branch name
git symbolic-ref --short HEAD

# Get commit hash
git rev-parse HEAD

# Check if commit exists
git cat-file -e abc123 2>/dev/null && echo "Exists"

# List all commits
git rev-list --all
```

**Recovery:**
```bash
# Find dangling commits
git fsck --lost-found

# Create branch from dangling commit
git branch recovered abc123
```

\newpage

## 20. Advanced Troubleshooting 🟢

Solve complex Git problems with confidence.

### Recovery Scenarios

**Scenario 1: Accidentally deleted branch**
```bash
# Find last commit
git reflog | grep branch-name

# Recreate branch
git branch branch-name abc123

# Or
git checkout -b branch-name HEAD@{2}
```

**Scenario 2: Lost commits after reset**
```bash
# Find lost commit
git reflog
git log --all --oneline | grep "commit message"

# Recover
git cherry-pick abc123
# Or
git reset --hard abc123
```

**Scenario 3: Corrupted repository**
```bash
# Check integrity
git fsck --full

# Clone from known good state
git clone --no-hardlinks /path/to/repo /path/to/backup

# Recover from reflog
git reflog expire --expire=now --all
git gc --prune=now
```

**Scenario 4: Detached HEAD with uncommitted work**
```bash
# Save work
git stash

# Return to branch
git checkout main

# Apply work
git stash pop
```

**Scenario 5: Merge conflicts nightmare**
```bash
# Abort merge
git merge --abort

# Or reset
git reset --hard HEAD

# Try again with strategy
git merge -X patience feature-branch
```

**Scenario 6: Wrong files committed**
```bash
# Remove from last commit
git rm --cached wrong-file.txt
git commit --amend --no-edit

# Remove from history (careful!)
git filter-repo --path wrong-file.txt --invert-paths
```

### Decision Trees

**"I messed up, what do I do?"**
```
Did you already push?
├─ NO → Safe to rewrite history
│   ├─ Last commit wrong? → git commit --amend
│   ├─ Last N commits wrong? → git rebase -i HEAD~N
│   └─ Everything wrong? → git reflog
│
└─ YES → Don't rewrite history
    ├─ New commit needed? → git revert
    ├─ Wrong branch? → git cherry-pick
    └─ Completely broken? → git revert + fresh start
```

**"Merge or Rebase?"**
```
Is this a shared branch?
├─ YES → MERGE (never rebase)
│
└─ NO → Feature branch?
    ├─ YES → REBASE before merging
    └─ NO → Personal preference
        ├─ Want clean history? → REBASE
        └─ Want true history? → MERGE
```

### Common Errors

**Error: "Your branch and 'origin/main' have diverged"**
```bash
# See what happened
git log HEAD..origin/main
git log origin/main..HEAD

# Option 1: Rebase (if not shared)
git rebase origin/main

# Option 2: Merge
git merge origin/main

# Option 3: Force (if you're sure)
git push --force-with-lease
```

**Error: "refusing to merge unrelated histories"**
```bash
# Allow merge
git merge --allow-unrelated-histories other-branch
```

**Error: "pathspec did not match any files"**
```bash
# File doesn't exist in Git
git ls-files | grep filename

# Check spelling
git status
```

**Error: "fatal: not a git repository"**
```bash
# Initialize repo
git init

# Or clone
git clone url
```

### Diagnostic Commands

```bash
# See what Git is doing
git status -vv

# Detailed diff
git diff --check

# Show configuration
git config --list --show-origin

# Show all commits
git log --all --oneline --graph

# Find when file was deleted
git log --all --full-history -- path/to/file

# Show branch history
git reflog show branch-name

# Verify repository
git fsck --full
```

\newpage

## 21. Command Reference Table

### Risk Level Guide

| Risk | Symbol | Meaning |
|------|--------|---------|
| 🟢 Safe | Green | No data loss risk |
| 🟡 Caution | Yellow | Can modify local history |
| 🔴 Danger | Red | Can permanently delete data |

### Command Quick Reference

| Command | Risk | Description |
|---------|------|-------------|
| `git log` | 🟢 | View commit history |
| `git reflog` | 🟢 | View HEAD history |
| `git status` | 🟢 | Check working tree status |
| `git diff` | 🟢 | View differences |
| `git blame` | 🟢 | Line-by-line attribution |
| `git show` | 🟢 | Show commit details |
| `git stash` | 🟢 | Save work temporarily |
| `git cherry-pick` | 🟢 | Apply specific commit |
| `git worktree` | 🟢 | Multiple working directories |
| `git bisect` | 🟢 | Binary search for bugs |
| `git commit --amend` | 🟡 | Modify last commit |
| `git rebase` | 🟡 | Rewrite commit history |
| `git rebase -i` | 🟡 | Interactive history editing |
| `git reset --soft` | 🟡 | Move HEAD, keep changes |
| `git reset --mixed` | 🟡 | Unstage changes |
| `git merge` | 🟡 | Combine branches |
| `git push --force-with-lease` | 🟡 | Safer force push |
| `git reset --hard` | 🔴 | Discard all changes |
| `git clean -fd` | 🔴 | Delete untracked files |
| `git branch -D` | 🔴 | Force delete branch |
| `git filter-branch` | 🔴 | Rewrite entire history |
| `git filter-repo` | 🔴 | Rewrite entire history |
| `git push --force` | 🔴 | Force push (dangerous) |
| `git reflog expire` | 🔴 | Remove reflog entries |
| `git gc --aggressive --prune=now` | 🔴 | Aggressive cleanup |

### Operation Safety Checklist

Before destructive operations:

- [ ] Create backup branch: `git branch backup`
- [ ] Check reflog is enabled: `git config core.logAllRefUpdates`
- [ ] Verify no uncommitted work: `git status`
- [ ] Confirm not on shared branch: `git branch -r --contains`
- [ ] Team notified if affecting others
- [ ] Have tested on feature branch first

\newpage

## 22. How to Export to PDF

### Using Pandoc (Recommended)

**Install Pandoc:**
```bash
# macOS
brew install pandoc

# Ubuntu/Debian
sudo apt-get install pandoc

# Windows
choco install pandoc
```

**Install LaTeX (for PDF):**
```bash
# macOS
brew install basictex

# Ubuntu/Debian
sudo apt-get install texlive

# Windows
choco install miktex
```

**Generate PDF:**
```bash
pandoc git-advanced-commands.md -o git-advanced-commands.pdf
```

**Advanced Options:**
```bash
# With table of contents
pandoc git-advanced-commands.md -o output.pdf --toc --toc-depth=2

# Custom margins
pandoc git-advanced-commands.md -o output.pdf -V geometry:margin=1in

# Different font size
pandoc git-advanced-commands.md -o output.pdf -V fontsize=11pt

# All together
pandoc git-advanced-commands.md -o output.pdf \
  --toc --toc-depth=2 \
  -V geometry:margin=2cm \
  -V fontsize=10pt \
  -V colorlinks=true
```

### Using VS Code

1. Install extension: "Markdown PDF"
2. Open `git-advanced-commands.md`
3. Right-click → "Markdown PDF: Export (pdf)"

### Using Markdown Viewers

**Typora:**
1. Open file in Typora
2. File → Export → PDF

**Marked 2 (macOS):**
1. Open file in Marked 2
2. File → Export → PDF

### Using GitHub

1. Push to GitHub repository
2. View file on GitHub
3. Print page (Ctrl+P)
4. Save as PDF

### Using Online Converters

- [Dillinger.io](https://dillinger.io/) - Markdown editor with PDF export
- [Markdown to PDF](https://www.markdowntopdf.com/) - Direct conversion

---

## Contributing

Found an error or want to suggest improvements? Please contribute!

**Repository:** [Your Repository URL]  
**Issues:** [Report issues here]  
**Pull Requests:** Always welcome!

---

## License

This document is provided as-is for educational purposes.  
Feel free to share, modify, and distribute with attribution.

---

## Acknowledgments

Created for the Data Engineering Team  
Based on official Git documentation and community best practices

**Stay Safe, Git Smart! 🚀**
