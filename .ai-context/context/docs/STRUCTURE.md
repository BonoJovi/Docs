# Documentation Directory Structure

**Last Updated**: 2025-12-10
**Purpose**: Guide to organizing documentation files
**Keywords**: structure, 構造, directory, ディレクトリ, organization, 整理, file hierarchy, ファイル階層, layout, レイアウト
**Related**: @CONVENTIONS.md, @I18N.md, @../workflows/GIT.md

---

## Overview

```
~/Data1/Docs/
├── CLAUDE.md              # AI context configuration
├── README.md              # Repository overview
├── LICENSE                # License file
├── .gitignore             # Git ignore rules
│
├── .ai-context/           # AI context files
│   ├── ESSENTIAL.md       # Essential info (auto-loaded)
│   ├── context/
│   │   ├── docs/          # Documentation guidelines
│   │   │   ├── CONVENTIONS.md
│   │   │   ├── I18N.md
│   │   │   └── STRUCTURE.md (this file)
│   │   ├── projects/      # Project references
│   │   │   └── RUST_PROJECTS.md
│   │   └── workflows/     # Workflow guides
│   │       ├── GIT.md
│   │       └── RELEASE.md
│   ├── knowledge/         # Methodology and philosophy
│   └── insights/          # Optional insights
│
├── ja/                    # Japanese documentation
│   ├── README.md
│   └── [topic]/
│       └── [document].md
│
├── en/                    # English documentation
│   ├── README.md
│   └── [topic]/
│       └── [document].md
│
└── draft/                 # Work-in-progress documents
    └── [draft-document].md
```

---

## Directory Purposes

### Root Level

**CLAUDE.md**:
- AI context configuration
- Minimal token usage strategy
- Points to on-demand contexts

**README.md**:
- Repository overview
- Quick start guide
- Navigation help

**LICENSE**:
- Copyright and usage terms

**.gitignore**:
- Files to exclude from version control

---

### .ai-context/

**Purpose**: AI-readable context files for Claude Code

**ESSENTIAL.md**:
- Auto-loaded at session start
- < 100 lines for token optimization
- Essential info only

**context/**:
- On-demand context files
- Organized by category (docs, projects, workflows)
- Loaded via `@` references

**knowledge/**:
- Methodology and philosophy
- Rarely needed for daily work
- Loaded when understanding "why" matters

**insights/**:
- Optional insights and observations
- Not required for operations
- Useful for deep understanding

---

### ja/ (Japanese Documentation)

**Structure**:
```
ja/
├── README.md              # Japanese docs overview
├── getting-started/       # 入門ガイド
│   ├── installation.md
│   └── quick-start.md
├── guides/                # ガイド
│   ├── user-guide.md
│   └── admin-guide.md
├── reference/             # リファレンス
│   ├── api-reference.md
│   └── cli-reference.md
└── tutorials/             # チュートリアル
    ├── tutorial-01.md
    └── tutorial-02.md
```

**Categories**:
- `getting-started/`: First-time user documentation
- `guides/`: How-to guides for specific tasks
- `reference/`: Technical reference materials
- `tutorials/`: Step-by-step learning paths

---

### en/ (English Documentation)

**Structure**: Same as `ja/` but in English

```
en/
├── README.md              # English docs overview
├── getting-started/
│   ├── installation.md
│   └── quick-start.md
├── guides/
│   ├── user-guide.md
│   └── admin-guide.md
├── reference/
│   ├── api-reference.md
│   └── cli-reference.md
└── tutorials/
    ├── tutorial-01.md
    └── tutorial-02.md
```

**Synchronization**: File structure should mirror `ja/`

---

### draft/ (Work in Progress)

**Purpose**: Temporary workspace for documents under development

**Rules**:
- ✅ Use for unfinished documents
- ✅ No structured subdirectories needed
- ✅ Move to `ja/` or `en/` when ready
- ❌ Do not commit incomplete drafts (unless collaborative)

**Workflow**:
```
1. Create draft/new-feature.md
2. Write and iterate
3. Review and finalize
4. Move to ja/guides/new-feature.md
5. Commit and push
```

---

## File Organization Patterns

### Pattern 1: By Topic

```
ja/
├── rust/
│   ├── basics.md
│   ├── ownership.md
│   └── lifetimes.md
├── tauri/
│   ├── setup.md
│   └── commands.md
└── testing/
    ├── unit-tests.md
    └── integration-tests.md
```

**When to use**: Technical documentation with clear categories

---

### Pattern 2: By Audience

```
ja/
├── user/
│   ├── installation.md
│   └── usage.md
├── developer/
│   ├── architecture.md
│   └── contributing.md
└── admin/
    ├── deployment.md
    └── maintenance.md
```

**When to use**: Multi-audience documentation

---

### Pattern 3: By Document Type

```
ja/
├── guides/
│   └── user-guide.md
├── reference/
│   └── api-reference.md
├── tutorials/
│   └── getting-started.md
└── faqs/
    └── common-issues.md
```

**When to use**: Mixed content types

---

## Naming Conventions

### Directories

```
✅ GOOD:
- getting-started/
- api-reference/
- user-guides/

❌ BAD:
- GettingStarted/
- API_Reference/
- user guides/ (space)
```

**Pattern**: `lowercase-with-hyphens/`

### Files

```
✅ GOOD:
- installation-guide.md
- api-reference.md
- quick-start.md

❌ BAD:
- Installation_Guide.md
- apiReference.md
- quick start.md (space)
```

**Pattern**: `lowercase-with-hyphens.md`

---

## Special Files

### README.md

**Purpose**: Directory-level overview

**Location**: Each major directory should have one

**Example** (`ja/README.md`):
```markdown
# 日本語ドキュメント

このディレクトリには日本語のドキュメントが含まれています。

## 目次

- [入門ガイド](getting-started/)
- [ユーザーガイド](guides/)
- [APIリファレンス](reference/)
```

### INDEX.md vs README.md

**README.md**: GitHub/GitLab displays automatically
**INDEX.md**: Custom index files (optional)

**Recommendation**: Use README.md for consistency

---

## .gitignore Recommendations

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

# Build artifacts (if any)
build/
dist/

# Drafts (optional - uncomment if drafts should not be committed)
# draft/
```

---

## Migration Guidelines

### Moving from draft/ to production

```bash
# 1. Finalize document
cd ~/Data1/Docs/draft
# Edit and review new-feature.md

# 2. Move to appropriate directory
mv new-feature.md ../ja/guides/

# 3. Commit
cd ~/Data1/Docs
git add ja/guides/new-feature.md
git commit -m "docs: add new feature guide (Japanese)"
git push
```

### Creating parallel language versions

```bash
# 1. Start with Japanese version
vim ja/guides/installation.md

# 2. Create English version
cp ja/guides/installation.md en/guides/installation.md

# 3. Translate
vim en/guides/installation.md

# 4. Commit both
git add ja/guides/installation.md en/guides/installation.md
git commit -m "docs: add installation guide (ja, en)"
git push
```

---

## Maintenance

### Regular Reviews

**Monthly**:
- [ ] Check for outdated information
- [ ] Update "Last Updated" dates
- [ ] Verify all links still work
- [ ] Remove deprecated documents

**Quarterly**:
- [ ] Reorganize if structure becomes unclear
- [ ] Archive old documents
- [ ] Update README files

---

## Scalability

### When to Create New Categories

**Threshold**: 5+ documents on the same topic

**Example**:
```
Before (4 documents):
ja/
└── guides/
    ├── rust-basics.md
    ├── rust-ownership.md
    ├── rust-lifetimes.md
    └── rust-traits.md

After (5+ documents):
ja/
├── guides/
└── rust/              ← New category
    ├── basics.md
    ├── ownership.md
    ├── lifetimes.md
    ├── traits.md
    └── macros.md
```

---

**Maintain a clean, logical structure to keep documentation accessible and maintainable.**
