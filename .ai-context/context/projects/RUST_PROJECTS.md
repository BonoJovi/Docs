# Rust Projects Reference

**Last Updated**: 2025-12-10
**Purpose**: Quick reference for Rust projects under ~/Data1/Linux/Projects/Rust/
**Keywords**: rust, projects, プロジェクト, reference, リファレンス, project list, プロジェクト一覧, context loading, コンテキスト読み込み
**Related**: @ESSENTIAL.md

---

## Project Directory

**Base Path**: `~/Data1/Linux/Projects/Rust/`

---

## Active Projects

### 1. Promps
**Path**: `~/Data1/Linux/Projects/Rust/Promps/`
**Description**: Japanese DSL prompt builder with Tauri + Blockly.js
**Status**: Active development (Phase 3 complete)
**Technologies**: Rust, Tauri, Blockly.js, HTML/CSS/JS
**AI Context**: `@~/Data1/Linux/Projects/Rust/Promps/.ai-context/ESSENTIAL.md`

**Key Features**:
- Visual block-based Japanese prompt construction
- Real-time preview
- Tauri-based desktop application

**Load Context**:
```
# Essential info only
@~/Data1/Linux/Projects/Rust/Promps/.ai-context/ESSENTIAL.md

# For coding work
@~/Data1/Linux/Projects/Rust/Promps/.ai-context/context/coding/CONVENTIONS.md
@~/Data1/Linux/Projects/Rust/Promps/.ai-context/context/coding/TESTING.md
@~/Data1/Linux/Projects/Rust/Promps/.ai-context/context/coding/API_STABILITY.md

# For architecture work
@~/Data1/Linux/Projects/Rust/Promps/.ai-context/context/architecture/PROJECT_STRUCTURE.md
```

---

### 2. KakeiBonByRust
**Path**: `~/Data1/Linux/Projects/Rust/KakeiBonByRust/`
**Description**: Household accounting application
**Status**: Production (525 tests, 100% passing)
**Technologies**: Rust, Tauri
**AI Context**: Check for `.ai-context/` in project directory

**Key Features**:
- Complete household accounting system
- Extensive test coverage
- Stable production codebase

---

### 3. konomachidenki
**Path**: `~/Data1/Linux/Projects/Rust/konomachidenki/`
**Description**: Municipality electricity project
**Status**: Active
**Technologies**: Rust
**AI Context**: Check for `.ai-context/` in project directory

---

### 4. Rustで作るプログラミング言語
**Path**: `~/Data1/Linux/Projects/Rust/Rustで作るプログラミング言語/`
**Description**: Learning project - implementing a programming language in Rust
**Status**: Learning/Educational
**Technologies**: Rust
**AI Context**: Check for `.ai-context/` in project directory

---

### 5. 実践Rustプログラミング入門 第2版
**Path**: `~/Data1/Linux/Projects/Rust/実践Rustプログラミング入門 第2版/`
**Description**: Practical Rust programming practice (2nd edition)
**Status**: Learning/Educational
**Technologies**: Rust
**AI Context**: Check for `.ai-context/` in project directory

---

## Loading Project Context

### Pattern 1: Load Essential Info Only

```
@~/Data1/Linux/Projects/Rust/[ProjectName]/.ai-context/ESSENTIAL.md
```

**When to use**: Quick reference, understanding project status

---

### Pattern 2: Load Specific Context

```
# For coding work
@~/Data1/Linux/Projects/Rust/[ProjectName]/.ai-context/context/coding/CONVENTIONS.md

# For architecture work
@~/Data1/Linux/Projects/Rust/[ProjectName]/.ai-context/context/architecture/PROJECT_STRUCTURE.md

# For workflows
@~/Data1/Linux/Projects/Rust/[ProjectName]/.ai-context/context/workflows/BRANCHING.md
```

**When to use**: Working on specific aspects of a project

---

### Pattern 3: Navigate to Project

```bash
cd ~/Data1/Linux/Projects/Rust/[ProjectName]
```

**When to use**: Direct work on project code, running tests, development

---

## Project Context Guidelines

### Before Loading Project Context:

1. **Identify the project** - Know which project you need context from
2. **Determine scope** - Do you need essential info only, or detailed context?
3. **Load minimal first** - Start with ESSENTIAL.md, load more as needed

### When Working on Multiple Projects:

1. **Keep contexts separate** - Load one project's context at a time
2. **Use explicit references** - Always use full path when referencing
3. **Minimize token usage** - Only load contexts you actively need

---

## Adding New Projects

When adding a new Rust project:

1. **Create project directory** under `~/Data1/Linux/Projects/Rust/`
2. **Add .ai-context/** structure to the project
3. **Update this file** with project information
4. **Commit changes** to this documentation repository

---

**This file provides quick reference for all Rust projects. Load project-specific contexts only when working on that project.**
