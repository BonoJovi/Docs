# Git Workflow for Documentation

**Last Updated**: 2025-12-10
**Purpose**: Git operations and best practices for documentation repository
**Keywords**: git, workflow, ワークフロー, version control, バージョン管理, commit, コミット, branch, ブランチ, merge, マージ, collaboration, 共同作業
**Related**: @../docs/CONVENTIONS.md, @RELEASE.md

---

## Repository Overview

**Location**: `~/Data1/Docs/`
**Type**: Git repository
**Purpose**: Version-controlled documentation management

---

## Basic Operations

### Check Status

```bash
cd ~/Data1/Docs
git status
```

**What it shows**:
- Modified files
- Untracked files
- Staged changes
- Current branch

---

### Stage Changes

**Stage specific file**:
```bash
git add ja/guides/installation.md
```

**Stage all changes in a directory**:
```bash
git add ja/
```

**Stage all changes**:
```bash
git add .
```

**Interactive staging**:
```bash
git add -p
```

---

### Commit Changes

**Basic commit**:
```bash
git commit -m "docs: add installation guide"
```

**Commit with detailed message**:
```bash
git commit -m "docs(ja): add installation guide

- Added step-by-step installation instructions
- Included troubleshooting section
- Added cross-references to related docs"
```

**Amend last commit** (if not pushed):
```bash
git add [forgotten-file]
git commit --amend --no-edit
```

---

### Push Changes

**Push to remote**:
```bash
git push
```

**Push specific branch**:
```bash
git push origin main
```

**Force push** (use with caution):
```bash
git push --force-with-lease
```

---

### Pull Changes

**Update from remote**:
```bash
git pull
```

**Pull with rebase**:
```bash
git pull --rebase
```

---

## Commit Message Conventions

### Format

```
<type>([scope]): <description>

[optional body]

[optional footer]
```

### Types

```
docs:      Documentation changes
fix:       Fix typos, broken links, errors
feat:      Add new documentation
refactor:  Reorganize structure
chore:     Maintenance tasks (update .gitignore, etc.)
```

### Scopes

```
(ja):      Japanese documentation
(en):      English documentation
(ja,en):   Both languages
(draft):   Draft documents
```

### Examples

**Simple**:
```
docs(ja): add installation guide
docs(en): update API reference
docs(ja,en): add troubleshooting section
fix: correct typo in README
```

**Detailed**:
```
docs(ja): add comprehensive testing guide

- Describe unit testing strategy
- Add integration testing examples
- Include CI/CD workflow
- Cross-reference with project testing docs
```

---

## Branching Strategy

### Main Branch

**Branch**: `main` or `master`
**Purpose**: Stable, published documentation
**Protection**: Can be protected (optional)

---

### Feature Branches

**When to use**:
- Large documentation additions
- Structural reorganization
- Collaborative writing

**Naming Pattern**:
```
docs/[topic]
docs/[feature]
```

**Examples**:
```
docs/api-reference
docs/restructure-guides
docs/add-tutorials
```

**Workflow**:
```bash
# Create feature branch
git checkout -b docs/api-reference

# Make changes
vim ja/reference/api-reference.md
git add ja/reference/api-reference.md
git commit -m "docs(ja): add API reference (WIP)"

# Continue working...
git add ...
git commit -m "docs(ja): complete API reference"

# Merge back to main
git checkout main
git merge --no-ff docs/api-reference

# Push
git push origin main

# Delete feature branch (optional)
git branch -d docs/api-reference
```

---

### Draft Branch (Optional)

**Branch**: `draft`
**Purpose**: Work-in-progress documents
**Merge**: Only when document is complete

---

## Collaboration Workflow

### Pattern 1: Direct Push

**When**: Small changes, solo work

```bash
git add .
git commit -m "docs: update installation guide"
git push
```

---

### Pattern 2: Feature Branch + Merge

**When**: Large changes, need review

```bash
# Create branch
git checkout -b docs/new-feature

# Work and commit
git add ...
git commit -m "docs: add new feature documentation"

# Push branch
git push -u origin docs/new-feature

# Merge locally (after review)
git checkout main
git merge --no-ff docs/new-feature
git push origin main
```

---

### Pattern 3: Pull Request (GitHub/GitLab)

**When**: Team collaboration, formal review

```bash
# Create branch
git checkout -b docs/comprehensive-guide

# Work and commit
git add ...
git commit -m "docs: add comprehensive guide"

# Push branch
git push -u origin docs/comprehensive-guide

# Create PR on GitHub/GitLab
# Review and merge via web interface
```

---

## Common Scenarios

### Scenario 1: Add New Document

```bash
cd ~/Data1/Docs

# Create document in draft first
vim draft/new-feature.md

# Review and finalize
# ... editing ...

# Move to appropriate directory
mv draft/new-feature.md ja/guides/

# If translating to English
vim en/guides/new-feature.md

# Stage both
git add ja/guides/new-feature.md en/guides/new-feature.md

# Commit
git commit -m "docs(ja,en): add new feature guide"

# Push
git push
```

---

### Scenario 2: Update Existing Document

```bash
cd ~/Data1/Docs

# Edit document
vim ja/guides/installation.md

# Check changes
git diff ja/guides/installation.md

# Stage and commit
git add ja/guides/installation.md
git commit -m "docs(ja): update installation guide

- Add new installation method
- Update version requirements
- Fix typos"

# Push
git push
```

---

### Scenario 3: Reorganize Structure

```bash
cd ~/Data1/Docs

# Create feature branch
git checkout -b docs/restructure

# Move files
git mv ja/guides/old-location.md ja/new-category/new-location.md

# Update cross-references
vim ja/README.md
# ... update links ...

# Commit
git add .
git commit -m "refactor: reorganize documentation structure

- Move guides to new categories
- Update all cross-references
- Update README navigation"

# Merge back
git checkout main
git merge --no-ff docs/restructure

# Push
git push origin main

# Clean up
git branch -d docs/restructure
```

---

### Scenario 4: Fix Typos Across Multiple Files

```bash
cd ~/Data1/Docs

# Edit multiple files
vim ja/guides/installation.md
vim ja/guides/usage.md
vim en/guides/installation.md

# Stage all changes
git add ja/guides/ en/guides/

# Commit
git commit -m "fix: correct typos across multiple guides"

# Push
git push
```

---

## Conflict Resolution

### When Conflicts Occur

**Scenario**: Two people edit the same file

**Steps**:
```bash
# Pull latest changes
git pull

# If conflict occurs:
# CONFLICT (content): Merge conflict in ja/guides/installation.md

# Edit conflicted file
vim ja/guides/installation.md

# Resolve conflicts (remove markers: <<<<<<<, =======, >>>>>>>)
# Keep desired changes

# Stage resolved file
git add ja/guides/installation.md

# Commit merge
git commit -m "merge: resolve conflicts in installation guide"

# Push
git push
```

---

## Viewing History

### Recent Commits

```bash
git log --oneline -10
```

### Commit Details

```bash
git show [commit-hash]
```

### File History

```bash
git log --follow ja/guides/installation.md
```

### Who Changed What

```bash
git blame ja/guides/installation.md
```

---

## Undoing Changes

### Discard Unstaged Changes

```bash
git checkout -- ja/guides/installation.md
```

### Unstage Changes

```bash
git reset HEAD ja/guides/installation.md
```

### Undo Last Commit (Keep Changes)

```bash
git reset --soft HEAD~1
```

### Undo Last Commit (Discard Changes)

```bash
git reset --hard HEAD~1
```

**⚠️ Warning**: `--hard` permanently deletes changes

---

## Best Practices

### 1. Commit Often

```
✅ Make small, logical commits
❌ One giant commit with all changes
```

### 2. Write Clear Messages

```
✅ docs(ja): add installation troubleshooting section
❌ update docs
```

### 3. Review Before Commit

```bash
# Always review changes before committing
git diff
git status
```

### 4. Pull Before Push

```bash
# Update local repository first
git pull
git push
```

### 5. Don't Commit Sensitive Data

```
❌ API keys
❌ Passwords
❌ Personal information
```

---

## .gitignore Strategy

**Current** (`.gitignore`):
```gitignore
# Temporary files
*.tmp
*.swp
*~

# Editor files
.vscode/
.idea/
*.sublime-*

# OS files
.DS_Store
Thumbs.db
```

**Optional** (uncommenting for draft exclusion):
```gitignore
# Exclude drafts from version control
draft/
```

---

## Git Aliases (Optional)

Add to `~/.gitconfig`:

```gitconfig
[alias]
    st = status
    co = checkout
    ci = commit
    br = branch
    lg = log --oneline --graph --all --decorate
    last = log -1 HEAD
    unstage = reset HEAD --
```

**Usage**:
```bash
git st         # Instead of git status
git lg         # Pretty log graph
git unstage .  # Unstage all
```

---

## Maintenance

### Check Repository Health

```bash
cd ~/Data1/Docs
git fsck
```

### Clean Up

```bash
# Remove untracked files (dry run)
git clean -n

# Remove untracked files (actual)
git clean -f

# Remove untracked files and directories
git clean -fd
```

---

## Related Workflows

**For Release Process**: See `@RELEASE.md`
**For Documentation Conventions**: See `@../docs/CONVENTIONS.md`

---

**Follow this workflow to maintain a clean, organized version control history.**
