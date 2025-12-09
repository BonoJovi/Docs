# AI Context - Essential Information (Docs Directory)

**Last Updated**: 2025-12-10
**Purpose**: Minimal context for Docs directory session startup (token optimization)
**Keywords**: essential, docs, documentation, ドキュメント, quick start, overview, status, current state, 現在の状態, エッセンシャル, 概要, quick reference, クイックリファレンス, entry point, starting point
**Related**: @README.md, @context/docs/STRUCTURE.md, @context/projects/RUST_PROJECTS.md

---

## Current Status

**Directory**: Documentation repository for technical writing
**Languages**: Japanese (ja/), English (en/), Draft (draft/)
**Version Control**: Git repository
**Purpose**: Centralized documentation management

---

## Directory Structure

```
~/Data1/Docs/
├── CLAUDE.md              # This AI context configuration
├── .ai-context/           # AI context files
│   ├── ESSENTIAL.md       # This file
│   ├── context/
│   │   ├── docs/          # Documentation guidelines
│   │   ├── projects/      # Project references
│   │   └── workflows/     # Git and release workflows
│   ├── knowledge/         # Methodology and philosophy
│   └── insights/          # Optional insights
├── ja/                    # Japanese documentation
├── en/                    # English documentation
└── draft/                 # Draft documents
```

---

## Critical Rules (3 Points Only)

### 1. Language Separation
- **Keep language-specific content in respective directories**
- Japanese → `ja/`
- English → `en/`
- Work-in-progress → `draft/`

### 2. Project Reference Pattern
- **For project-specific context, reference source project's .ai-context/**
- Rust projects: `~/Data1/Linux/Projects/Rust/[ProjectName]/.ai-context/`
- Available projects: See `@.ai-context/context/projects/RUST_PROJECTS.md`

### 3. Documentation Workflow
- **All documents should be version controlled with Git**
- Commit frequently with descriptive messages
- Details: `@.ai-context/context/workflows/GIT.md`

---

## Available Rust Projects

**Project Locations**: `~/Data1/Linux/Projects/Rust/`

**Active Projects**:
1. **Promps** - Japanese DSL prompt builder (Tauri + Blockly)
2. **KakeiBonByRust** - Household accounting application
3. **konomachidenki** - Municipality electricity project
4. **Rustで作るプログラミング言語** - Programming language implementation
5. **実践Rustプログラミング入門 第2版** - Rust programming practice

**To Reference Project Context**:
```
Example: Load Promps project context
@~/Data1/Linux/Projects/Rust/Promps/.ai-context/ESSENTIAL.md
```

---

## Quick File Locations

**Documentation**:
- Japanese: `~/Data1/Docs/ja/`
- English: `~/Data1/Docs/en/`
- Drafts: `~/Data1/Docs/draft/`

**Project References**:
- Rust projects: `~/Data1/Linux/Projects/Rust/`

---

## When You Need More Context

**For Documentation Work**:
- Conventions: `@.ai-context/context/docs/CONVENTIONS.md`
- i18n Guidelines: `@.ai-context/context/docs/I18N.md`
- Structure: `@.ai-context/context/docs/STRUCTURE.md`

**For Project-Specific Work**:
- Rust Projects List: `@.ai-context/context/projects/RUST_PROJECTS.md`
- Load project's own `.ai-context/` files directly

**For Git Workflows**:
- Git Operations: `@.ai-context/context/workflows/GIT.md`

---

## Common Operations (Quick Reference)

**Check documentation status**:
```bash
cd ~/Data1/Docs && git status
```

**Reference a Rust project**:
```bash
# View project structure
ls -la ~/Data1/Linux/Projects/Rust/Promps/

# Load project context (in Claude Code)
@~/Data1/Linux/Projects/Rust/Promps/.ai-context/ESSENTIAL.md
```

**Create new documentation**:
```bash
# Draft first
cd ~/Data1/Docs/draft && touch new-doc.md

# Then move to appropriate language directory
mv new-doc.md ../ja/
```

**Git workflow**:
```bash
cd ~/Data1/Docs
git add .
git commit -m "docs: description of changes"
git push
```

---

**This file loads < 100 lines. Load other contexts only when needed.**
