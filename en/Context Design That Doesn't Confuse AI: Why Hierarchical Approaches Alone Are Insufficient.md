# Context Design That Doesn't Confuse AI: Why Hierarchical Structure Alone Isn't Enough

> **Author**: Yoshihiro NAKAHARA
> **About This Article**: Based on observations and experimental data from the author's development project, this document details optimization techniques performed by AI (Claude), documented by Claude itself.
> **Responsible for Content**: Yoshihiro NAKAHARA

## Introduction: The “Uneasiness” After Hierarchization

We organized the project's AI context and transitioned to a hierarchical structure for token efficiency.

```
.ai-context/
├── ESSENTIAL.md # [Tier 1] Always loaded
├── context/ # [Tier 2] Loaded when needed
│ ├── RELEASE.md
│ ├── BRANCHING.md
│ └── TESTING.md
└── knowledge/ # [Tier 3] Detailed Information
└── METHODOLOGY.md
```

Token consumption improved dramatically (9% → 2-3%). We were satisfied.

**However, a few days later, while observing a development session, we noticed something. 
“Huh? The AI is searching for the same file over and over...?”

### Observed Phenomenon

**During Release Work**:
```
AI: Searching for “release”... (10 files found)
AI: Searching for ‘deploy’... (3 files found)
AI: Searching for “version”... (9 files found)
AI: Finally opening RELEASE.md
```

**During branch strategy verification**:
```
AI: Searching for “branch”... (9 files found)
AI: Searching for “persistent”... (6 files found)
AI: Unable to find a good match, opening multiple files to check
```

Even though we reduced files by organizing hierarchically, **the number of searches increased**. This defeats the purpose.

### The Core Issue

Token consumption decreased. But, **the AI can't find the target files**.

Why?
- File names alone don't reveal content
- Searching Japanese keywords fails to find files with English names
- Inadequate handling of technical term diversity (“release” = “deploy” = ‘publish’ = “リリース”)

**Hierarchical organization alone does not solve the searchability problem.**

This article introduces the **keyword metadata system** we implemented after recognizing this issue, and its surprising effectiveness.

## Prerequisite: Why AI Context is Necessary

AI development tools read context files to understand project-specific rules, conventions, and architecture.

**Typical Context File Structure**:
```
.ai-context/
├── README.md
├── CONVENTIONS.md
├── ARCHITECTURE.md
├── TESTING.md
└── RELEASE.md
```

However, as projects grow, context files also become bloated. Reading all files every time **consumes a large amount of token budget**.

## Common Solution: Hierarchical Structure

Many projects reduce token consumption by organizing context hierarchically.

```
.ai-context/
├── ESSENTIAL.md # [Tier 1] Always loaded (< 100 lines)
├── context/ # [Tier 2] Loaded when needed
│ ├── coding/
│ │ ├── CONVENTIONS.md
│ │ └── TESTING.md
│ └── workflows/
│ ├── RELEASE.md
│ └── BRANCHING.md
└── knowledge/ # [Tier 3] Detailed design philosophy
└── METHODOLOGY.md
```

**Effect**:
- Token consumption at session start: ~9% → Reduced to ~2-3%
- Only necessary files loaded on demand

This is indeed effective. However, **a new problem arises**.

## Problem: AI gets confused even with layering

Even after improvements through hierarchical organization, the following phenomenon was observed during actual development sessions:

**Case 1: During Release Work**
```
AI: I want to check the release procedure...
→ Search for “release” (10 files found)
→ Search for ‘deploy’ (3 files found)
→ Search for “version” (9 files found)
→ Finally discovered RELEASE.md
```

**Case 2: Checking Branch Strategy**
```
AI: I want to check the persistent branch rules...
→ Search for “branch” (9 files found)
→ Search for ‘persistent’ (6 files found - but too few hits)
→ Search for “永続” (Finally found BRANCHING.md)
```

### Why does this problem occur?

1. **File names alone don't reveal content**
- `RELEASE.md` doesn't contain terms like “deploy” or “CI/CD” (in the filename)
- Searching with Japanese keywords fails to find files with English names

2. **Unclear relationships between related files**
- Release tasks involve RELEASE.md, TESTING.md, and BRANCHING.md
- But understanding this requires reading all files

3. **Variety of technical terms**
- “release” = “deploy” = “publish” = ‘リリース’ = “デプロイ”
- All expressions should be searchable, but reality only captures one

**Result**: Repeated searches waste tokens unnecessarily.

## Solution: Search-Enhancing Keyword Metadata

Beyond hierarchical organization, **embedding search metadata in each file** dramatically improved AI search accuracy.

### System Design

Add the following metadata at the start of each context file:

```markdown
# Release Process

**Last Updated**: 2025-12-06
**Purpose**: Step-by-step release procedure for Promps project
**Keywords**: release, deploy, deployment, publish, リリース, デプロイ, 公開, version bump, バージョンアップ, tagging, git tag, tag, GitHub Actions, workflow, CI/CD, automated build, draft release, artifacts, binaries, rollback
**Related**: @BRANCHING.md, @TESTING.md, @GITHUB_PROJECTS.md
```

### Metadata Components

#### 1. Keywords (Search Keywords)

**Keywords to Include**:
- **Primary Technical Terms** (English): release, deploy, CI/CD
- **Japanese Translations**: リリース, デプロイ, 自動ビルド
- **Synonyms/Related Terms**: publish, tagging, version bump
- **Specific Tools/Commands**: GitHub Actions, git tag, cargo test

**Guidelines**:
- Include both English and Japanese (bilingual support)
- Cover multiple expressions for the same concept
- Include specific terms likely to be searched

#### 2. Related (Related Files)

**Cross-referencing using `@` notation**:
```markdown
**Related**: @BRANCHING.md, @TESTING.md
```

This helps the AI understand that “release tasks are also related to branch strategy and testing.”

### Implementation Example: Application to Key Files

#### RELEASE.md (Release Procedure)
```markdown
**Keywords**: release, deploy, deployment, publish, リリース, デプロイ, 公開, version bump, バージョンアップ, tagging, git tag, タグ, GitHub Actions, workflow, CI/CD, automated build, draft release, artifacts, binaries, publish release, rollback
**Related**: @BRANCHING.md, @TESTING.md, @GITHUB_PROJECTS.md
```

#### BRANCHING.md (Branch Strategy)
```markdown
**Keywords**: branching, branch strategy, persistent branches, feature branch, git workflow, merge, no-delete, layered architecture, phase branches, dev branch, integration branch
* *Related**: @RELEASE.md, @API_STABILITY.md, @CONVENTIONS.md
```

#### TESTING.md (Testing Strategy)
```markdown
**Keywords**: testing, tests, test-driven, TDD, unit tests, integration tests, cargo test, npm test, test coverage, backend tests, frontend tests, test strategy, quality assurance, QA
**Related**: @RELEASE.md, @CONVENTIONS.md, @API_STABILITY.md
```

#### API_STABILITY.md (API Stability Policy)
```markdown
**Keywords**: API stability, immutable API, backward compatibility, breaking changes, Phase 0, core layer, core layer, function signature, versioning, deprecation, additive changes, no modification
**Related**: @BRANCHING.md, @CONVENTIONS.md, @TESTING.md
```

## Effect Measurement: Before/After

### Before (Before Keyword Addition)

**Searching During Release Work**:
```
AI: Search for “release” → 10 files found (couldn't narrow down)
AI: Search for “GitHub Actions” → No hits (not in filenames)
AI: Search for “タグ” → No hits (Japanese keywords not supported)
AI: Had to open multiple files to check → Token consumption
```

### After (After Keyword Addition)

**Same Release Task Search**:
```
AI: Search for “release” → RELEASE.md clearly tops results
AI: Search for “deploy” → RELEASE.md hits immediately
AI: Search for “リリース” → RELEASE.md found (Japanese supported)
AI: Search for “CI/CD” → RELEASE.md found (related keyword)
```

### Quantitative Improvement

**Project**: Promps (Rust + Tauri desktop app)
**Context Files**: 20 files (total ~8,000 lines)

| Metric | Before | After | Improvement Rate |
|------|--------|-------|--------|
| Files with Keywords | 0 | 19 | - |
| Search Accuracy (Hits Target File on First Try) | ~30% | ~90% | **3x Improvement** |
| Average Search Attempts (During Release Work) | 3-4 | 1 | **75% Reduction** |
| Unnecessary File Loading | High | Minimal | Saves Tokens |

### Observations During Actual Development Sessions

**During v0.0.3-1 Release Work** (2025-12-09):
- Before keyword addition: AI repeatedly searched multiple times to find the same file
- After keyword addition: Able to discover the correct file on the first search

## Ready to Use: Templates and Best Practices

### Basic Template

```markdown
# [File Title]

**Last Updated**: YYYY-MM-DD
**Purpose**: [One-line description of this file's purpose]
**Keywords**: [English Keyword 1], [English Keyword 2], [Japanese 1], [Japanese 2], [Technical Term], [Tool Name], [Command Name]
**Related**: @[Related File 1].md, @[Related File 2].md

---

[Main content starts here]
```

### Best Practices for Keyword Selection

#### 1. Cover all categories

**Topic-based** (What is it about?):
- English: release, testing, architecture
- Japanese: リリース, テスト, アーキテクチャ
- Synonyms: deploy, QA, design

**Technical** (What to use?):
- Tools: GitHub Actions, Tauri, Rust
- Commands: cargo test, git tag, npm install
- Concepts: CI/CD, TDD, DDD

**Workflow** (How?):
- Process: workflow, pipeline, branching strategy
- Japanese: ワークフロー, 手順, フロー

#### 2. Include search-friendly examples

**Good**:
```markdown
**Keywords**: release, deploy, GitHub Actions, git tag, version bump, リリース, CI/CD
```

**Better**:
```markdown
**Keywords**: release, deploy, deployment, publish, リリース, デプロイ, 公開, version bump, バージョンアップ, tagging, git tag, タグ, GitHub Actions, workflow, CI/CD, automated build, draft release, artifacts, binaries, rollback
```

Why is **Better**?
- Covers both “deployment” and “deploy”
- Adds the alternative Japanese expression ‘公開’ (publication)
- Includes specific related terms like “automated build” and “artifacts”

#### 3. Always include both Japanese and English

**Reason**:
- AI often thinks in English → English keywords required
- Japanese projects may be searched in Japanese → Japanese keywords required
- Bilingual support doubles search accuracy

#### 4. Network related files with `Related`

**Principles**:
- Link files frequently referenced together
- Link files with dependencies
- Create mutual references (A → B, B → A)

**Example**:
```markdown
# RELEASE.md
**Related**: @BRANCHING.md, @TESTING.md, @GITHUB_PROJECTS.md

# BRANCHING.md
**Related**: @RELEASE.md, @API_STABILITY.md, @CONVENTIONS.md

# TESTING.md
**Related**: @RELEASE.md, @CONVENTIONS.md, @API_STABILITY.md
```

This allows the AI to understand that “release tasks are related to branch strategy and testing.”

### Implementation Steps

#### Step 0: Recommended Approach - Leave it to the AI (Most Effective)

**Important**: We **strongly recommend** letting the AI handle this task instead of humans doing it manually.

**Why should we let the AI handle it?**

1. **The AI understands best**
- As the searcher, the AI knows what's easiest to search for
- The AI's own judgment of “I want to search for this keyword” is more accurate than humans guessing “this keyword might be searched”

2. **Diversity of Search Patterns**
- AI often thinks in English but also searches in Japanese
- Searches for the same concept using multiple expressions (e.g., “release” → ‘deploy’ → “publish”)
- Humans struggle to cover all these patterns

3. **Continuous Improvement**
- AI can attempt actual searches and recognize keywords that didn't yield results
- Based on this feedback, it can add more search-friendly keywords

**Specific Implementation Method**:

```
# Ask your AI development tool (Claude Code, Cursor, Copilot, etc.) to do the following

"Read this article (draft_qiita_article.md) and
add Keywords and Related sections to all files in the
.ai-context/ directory.

Requirements:
1. Understand each file's content and select search-friendly keywords
2. Include both English and Japanese
3. Cover synonyms and related terms
4. Include specific technical terms, tool names, and command names
5. Cross-reference related files in the Related section

Template:
**Keywords**: [English], [Japanese], [Synonyms], [Technical Terms]
**Related**: @[Related File].md
"
```

**Actual Example (Promps Project)**:
```
Developer: “It seems you've been repeatedly searching without finding data during code edits and release work from yesterday to today. Please add keywords to improve searchability.”

AI: “Understood. I'll add keywords to 19 files.”
→ Completed adding appropriate keywords to all files in about 40 minutes
→ Search accuracy dramatically improved (30% → 90%)
```

**Benefits of delegating to AI**:
- ⏱️ Time savings: Significantly faster than manual human effort
- 🎯 High accuracy: Keyword selection reflecting AI search patterns
- 🔄 Consistency: Uniform format across all files
- 🚀 Immediate effect: Searchability improves instantly after implementation

Of course, manual implementation is also possible. The steps are outlined below.

#### Step 1: Inventory Existing Files (Manual Approach)

```bash
find .ai-context -name “*.md” -type f | sort
```

#### Step 2: Add Metadata to Each File

**Priority Order**:
1. **High-Frequency Files**: RELEASE, BRANCHING, TESTING (used every time)
2. **Medium-frequency files**: CONVENTIONS, API_STABILITY (used during coding)
3. **Low-frequency files**: METHODOLOGY, DESIGN_PHILOSOPHY (only when understanding is needed)

#### Step 3: Verify searchability

```bash
# Test searching with grep
grep -r “deploy” .ai-context --include=“*.md”
grep -r ‘リリース’ .ai-context --include=“*.md”
grep -r “CI/CD” .ai-context --include=“*.md”
```

Success is confirmed if the target file appears at the top of the results.

#### Step 4: Add Search Guide to README.md

```markdown
## Keyword Search System

**Purpose**: All context files now include searchable keywords for better discoverability.

### Searching for Context

**By topic (English/Japanese)**:
- Release process: `release`, `deploy`, `リリース`, `デプロイ`
- Branching: `branch`, `merge`, `ブランチ`, `マージ`, `persistent`
- Testing: `test`, `テスト`, `cargo test`, `TDD`

**By technology**:
- `Tauri`, `Rust`, `Blockly`, `WebView`
- `GitHub Actions`, `CI/CD`, `workflow`

**By workflow**:
- `git`, `commit`, `tag`, `push`
- `version bump`, `バージョンアップ`
```

## Application to Other AI Development Tools

This keyword metadata system is not dependent on specific AI tools.

**Applicable Tools**:
- Claude Code
- GitHub Copilot
- Cursor (`.cursorrules` file)
- Windsurf
- Cline (formerly Claude Dev)
- Continue
- Other LLM-integrated IDEs

**How to Apply**:
1. Add Keywords/Related to each tool's context file
2. Follow tool-specific filename conventions
- Claude Code: `.ai-context/*.md`
- Cursor: `.cursorrules`
- Continue: `.continue/context/*.md`

## Summary

### Hierarchical Structure + Keyword Metadata = Optimal AI Context

**Effects of Hierarchical Structure**:
- ✅ Reduced token consumption (9% → 2-3%)
- ✅ Loaded only necessary files

**Effects of Keyword Metadata**:
- ✅ Improved search accuracy (30% → 90%)
- ✅ Reduced search attempts (3-4 → 1)
- ✅ Minimized unnecessary file loading

**Combining both approaches**:
- 🎯 Development efficiency Up
- 🎯 Token consumption Down
- 🎯 Smoother collaboration with AI

### Implementation Cost vs. Effect

**Implementation Cost**:
- Adding keywords to 19 files: ~30 minutes
- Adding search guide to README.md: ~10 minutes
- Total: **~40 minutes one-time effort**

**Effect**:
- Benefits apply to all subsequent sessions
- Searchability remains as project grows
- All team members benefit

The return on investment is exceptionally high.

### Next Steps

#### Recommended: How to Handle It with AI

1. **Feed this article to the AI**
```
“Read this article and add
Keywords and Related to my project's .ai-context/ directory.”
```

2. **Observe the AI's work**
- Verify which keywords it selected
- Confirm the cross-references in Related are appropriate

3. **Measure Effectiveness**
- Observe search frequency during the next development session
- Check if “Huh? The AI is searching again” instances decreased

4. **Provide Feedback**
- “This file still seems hard to search, so add keywords” 
- AI continuously improves

#### Manual Approach

For those preferring manual work, follow these steps:

1. **Start small**: Add keywords to just one file
2. **Verify effectiveness**: Observe if AI search accuracy improves
3. **Scale up**: Apply to all files if effective
4. **Improve**: Add or adjust keywords for better searchability

---

**AI context optimization doesn't end with just hierarchical organization.**

Enhancing searchability creates an environment where the AI truly “never gets lost.”

And by **entrusting the optimization work itself to the AI**, you achieve maximum effectiveness.

## Reference Resources

**Actual Project Example**:
- [Promps - Visual Prompt Builder](https://github.com/BonoJovi/Promps)
- Applied keyword metadata to 19 files
- Implementation can be verified in the `.ai-context/` directory

---

We hope this article helps make your AI development smoother.

Questions or feedback? We welcome them in the comments or via [GitHub Issue](https://github.com/BonoJovi/Docs/issues)!
