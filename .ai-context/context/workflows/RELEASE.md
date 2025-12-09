# Documentation Release Workflow

**Last Updated**: 2025-12-10
**Purpose**: Guide for publishing and versioning documentation
**Keywords**: release, リリース, publish, 公開, versioning, バージョニング, deployment, デプロイ, documentation release, ドキュメントリリース
**Related**: @GIT.md, @../docs/CONVENTIONS.md

---

## Overview

**Documentation Release** = Making documentation publicly available and marking it as stable.

---

## Release Criteria

### Document is Ready for Release When:

- [x] Content is complete and accurate
- [x] All code examples are tested and working
- [x] No spelling or grammar errors
- [x] Links verified (no broken links)
- [x] Consistent formatting throughout
- [x] Reviewed (self-review or peer review)
- [x] Translations synchronized (if applicable)
- [x] Status updated to "Published"

---

## Release Workflow

### Step 1: Pre-Release Check

**Verify Document Quality**:
```bash
cd ~/Data1/Docs

# Check for broken links (if using link checker)
# Example: markdown-link-check ja/guides/installation.md

# Check for typos (if using spell checker)
# Example: aspell check ja/guides/installation.md

# Review changes
git diff
```

**Update Metadata**:
```markdown
# Installation Guide

**Last Updated**: 2025-12-10
**Status**: Published  ← Update this
**Version**: 1.0       ← Add version if applicable
```

---

### Step 2: Commit Changes

**Pattern**:
```bash
git add ja/guides/installation.md
git commit -m "docs(ja): publish installation guide v1.0"
```

---

### Step 3: Tag Release (Optional)

**For Major Documentation Updates**:
```bash
# Create annotated tag
git tag -a docs-v1.0 -m "Release: Documentation v1.0

- Added installation guide
- Updated API reference
- Completed user guide"

# Push tag
git push origin docs-v1.0
```

**View Tags**:
```bash
git tag -l
```

---

### Step 4: Push to Remote

```bash
git push origin main
```

---

### Step 5: Publish (If Using Documentation Platform)

**If using GitHub Pages, ReadTheDocs, etc.**:
```bash
# Build documentation (example)
# mkdocs build
# hugo build

# Deploy (example)
# mkdocs gh-deploy
```

---

## Versioning Strategy

### Semantic Versioning for Documentation

**Pattern**: `vMAJOR.MINOR.PATCH`

**MAJOR**: Structural changes, major rewrites
**MINOR**: New sections, significant additions
**PATCH**: Typo fixes, minor updates

**Examples**:
```
v1.0.0: Initial complete documentation
v1.1.0: Added new troubleshooting section
v1.1.1: Fixed typos in installation guide
v2.0.0: Complete restructure of documentation
```

---

### Document-Level Versioning

**Add to Document Frontmatter**:
```markdown
**Version**: 1.0
**Last Updated**: 2025-12-10
```

---

### Repository-Level Versioning

**Using Git Tags**:
```bash
git tag -a docs-v1.0 -m "Documentation Release v1.0"
git push origin docs-v1.0
```

---

## Release Types

### Type 1: Individual Document Release

**Scenario**: Publishing a single new document

**Workflow**:
```bash
# 1. Finalize document
vim ja/guides/new-feature.md

# 2. Update metadata
# **Status**: Published
# **Version**: 1.0

# 3. Commit
git add ja/guides/new-feature.md
git commit -m "docs(ja): publish new feature guide v1.0"

# 4. Push
git push
```

---

### Type 2: Documentation Set Release

**Scenario**: Publishing a complete set of related documents

**Workflow**:
```bash
# 1. Finalize all documents
vim ja/guides/installation.md
vim ja/guides/usage.md
vim ja/guides/troubleshooting.md

# 2. Update metadata in all files
# **Status**: Published
# **Version**: 1.0

# 3. Commit together
git add ja/guides/
git commit -m "docs(ja): publish complete user guide set v1.0

- Installation guide
- Usage guide
- Troubleshooting guide"

# 4. Tag release
git tag -a user-guide-v1.0 -m "User Guide v1.0 Release"

# 5. Push with tags
git push origin main --tags
```

---

### Type 3: Major Documentation Release

**Scenario**: Major version release with many changes

**Workflow**:
```bash
# 1. Create release branch
git checkout -b release/v2.0

# 2. Finalize all changes
# ... review and edit ...

# 3. Update all "Last Updated" and "Version" fields
# ... bulk update ...

# 4. Commit
git add .
git commit -m "docs: prepare v2.0 release

- Updated all documentation to v2.0
- Restructured guides section
- Added new tutorials
- Synchronized ja/en translations"

# 5. Merge to main
git checkout main
git merge --no-ff release/v2.0

# 6. Tag release
git tag -a docs-v2.0 -m "Documentation v2.0 Release

Major Changes:
- Complete restructure of guides
- 10 new tutorials added
- Updated all screenshots
- Synchronized all translations"

# 7. Push
git push origin main --tags

# 8. Clean up (keep release branch for reference)
# git branch -d release/v2.0  # Optional
```

---

## Release Checklist

### Before Release

- [ ] All documents complete
- [ ] Code examples tested
- [ ] Spell check completed
- [ ] Link verification completed
- [ ] Cross-references accurate
- [ ] Translations synchronized
- [ ] Metadata updated (Status, Version, Date)
- [ ] Reviewed (self or peer)
- [ ] Git status clean

### During Release

- [ ] Changes committed
- [ ] Commit message descriptive
- [ ] Tag created (if applicable)
- [ ] Pushed to remote
- [ ] Published to platform (if applicable)

### After Release

- [ ] Release announced (if applicable)
- [ ] Feedback channels open
- [ ] Monitor for issues
- [ ] Plan next release

---

## Rollback Strategy

### If Release Has Issues

**Scenario**: Published documentation contains errors

**Option 1: Quick Fix**:
```bash
# Fix error
vim ja/guides/installation.md

# Commit fix
git commit -am "docs(ja): fix critical error in installation guide"

# Update version (patch)
# **Version**: 1.0.1

# Push
git push
```

**Option 2: Revert Release**:
```bash
# Revert to previous state
git revert [commit-hash]

# Mark as draft
# **Status**: Draft

# Push
git push

# Fix issues
# ... editing ...

# Re-release
git commit -am "docs(ja): re-publish installation guide v1.0.1"
git push
```

---

## Deprecation Policy

### Marking Documents as Deprecated

**When**: Document is outdated but still referenced

**How**:
```markdown
# Old Feature Guide

**Status**: Deprecated
**Deprecated Since**: 2025-12-10
**Replacement**: See [New Feature Guide](new-feature.md)

---

> ⚠️ **Deprecation Notice**
>
> This document is deprecated and will be removed in v3.0.
> Please refer to the [New Feature Guide](new-feature.md) for current information.

---

[Old content continues...]
```

---

### Removing Deprecated Documents

**Workflow**:
```bash
# 1. Ensure replacement is published
git log ja/guides/new-feature.md

# 2. Archive old document (optional)
mkdir -p archive/deprecated
git mv ja/guides/old-feature.md archive/deprecated/

# 3. Update cross-references
# ... update links to point to new document ...

# 4. Commit
git commit -m "docs: archive deprecated old feature guide

Replaced by new-feature.md in v2.0"

# 5. Push
git push
```

---

## Release Announcement

### Internal Announcement

**Example** (for team):
```markdown
## Documentation Release v1.0

We've published the complete user guide set:

- Installation Guide (ja/en)
- Usage Guide (ja/en)
- Troubleshooting Guide (ja/en)

All guides are now available at:
https://[your-docs-url]/

Feedback welcome!
```

### External Announcement

**Example** (for users):
```markdown
## New Documentation Available

We're pleased to announce the release of comprehensive user documentation:

**What's New**:
- Step-by-step installation guide
- Complete usage guide with examples
- Troubleshooting guide for common issues

**Languages**: Japanese, English

**Access**: [Documentation URL]

Let us know if you have any questions!
```

---

## Continuous Documentation

### Philosophy

**Documentation is Never "Done"**:
- User feedback reveals gaps
- Software evolves
- Best practices change
- New use cases emerge

**Continuous Improvement Cycle**:
```
Release → Feedback → Improve → Release
```

---

### Iteration Strategy

**Regular Updates**:
- Weekly: Fix typos, broken links
- Monthly: Update for software changes
- Quarterly: Add new sections based on feedback
- Yearly: Major restructure or rewrite

---

## Metrics (Optional)

### Track Documentation Quality

**Metrics to Consider**:
- Number of broken links
- User feedback (positive/negative)
- Time since last update
- Coverage (% of features documented)

**Example Tracking**:
```markdown
# Documentation Health Report

**Date**: 2025-12-10
**Total Documents**: 25
**Last Updated < 1 month**: 20 (80%)
**Broken Links**: 0
**Pending Updates**: 3
**User Feedback Score**: 4.5/5
```

---

## Related Workflows

**For Git Operations**: See `@GIT.md`
**For Documentation Conventions**: See `@../docs/CONVENTIONS.md`

---

**Release documentation regularly to keep information current and valuable.**
