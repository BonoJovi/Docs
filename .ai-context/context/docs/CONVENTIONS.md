# Documentation Conventions

**Last Updated**: 2025-12-10
**Purpose**: Guidelines for creating and maintaining documentation
**Keywords**: documentation, ドキュメント, conventions, 規約, guidelines, ガイドライン, style guide, スタイルガイド, formatting, フォーマット, markdown, best practices, ベストプラクティス
**Related**: @I18N.md, @STRUCTURE.md, @../workflows/GIT.md

---

## File Format

**Standard Format**: Markdown (.md)

**Why Markdown**:
- ✅ Version control friendly (plain text)
- ✅ Easy to read and write
- ✅ Wide tooling support
- ✅ GitHub/GitLab compatible

---

## File Naming

### General Rules

```
✅ GOOD:
- api-reference.md
- getting-started.md
- troubleshooting-guide.md

❌ BAD:
- API_Reference.md (mixed case)
- getting started.md (spaces)
- guide.txt (wrong extension)
```

**Pattern**: `lowercase-with-hyphens.md`

### Language-Specific Files

```
ja/api-reference.md    # Japanese version
en/api-reference.md    # English version
```

**Rule**: Same filename across language directories

---

## Document Structure

### Standard Template

```markdown
# Document Title

**Last Updated**: YYYY-MM-DD
**Author**: [Your Name] (optional)
**Status**: Draft | Review | Published

---

## Overview

Brief description of what this document covers.

---

## Section 1

Content here.

### Subsection 1.1

More detailed content.

---

## Section 2

Continue structuring content logically.

---

## References

- [Link 1](URL)
- [Link 2](URL)
```

### Header Levels

```markdown
# H1: Document Title (only one per document)

## H2: Major Sections

### H3: Subsections

#### H4: Minor Subsections (use sparingly)
```

**Rule**: Never skip header levels (H1 → H3 without H2)

---

## Formatting Guidelines

### Code Blocks

**With Language Specifier**:
```markdown
```rust
fn main() {
    println!("Hello, world!");
}
``` (close backticks)
```

**Inline Code**: Use \`backticks\` for inline code: `variable_name`

### Lists

**Unordered**:
```markdown
- Item 1
- Item 2
  - Nested item 2.1
  - Nested item 2.2
```

**Ordered**:
```markdown
1. First step
2. Second step
3. Third step
```

### Emphasis

```markdown
**Bold** for strong emphasis
*Italic* for mild emphasis
~~Strikethrough~~ for deprecated content
```

### Links

```markdown
[Link text](URL)
[Link text](URL "Tooltip")
[Internal reference](#section-name)
```

### Tables

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Data 1   | Data 2   | Data 3   |
| Data 4   | Data 5   | Data 6   |
```

---

## Language Guidelines

### Japanese Documentation

**Tone**: Polite but concise (です・ます調)

**Technical Terms**: Use katakana for well-known English terms
```
✅ ファイル (file)
✅ ディレクトリ (directory)
✅ コミット (commit)

❌ フィル (weird transliteration)
❌ コミットする事 (too verbose)
```

**Code Examples**: Keep comments in Japanese
```rust
// ユーザー情報を取得
fn get_user() -> User {
    // ...
}
```

### English Documentation

**Tone**: Professional and clear

**Avoid**:
- Jargon without explanation
- Overly casual language
- Ambiguous pronouns ("it", "this", "that" without clear reference)

**Prefer**:
- Active voice: "The function returns..." (not "...is returned by")
- Clear subjects: "The parser handles..." (not "It handles...")
- Examples after explanations

---

## Cross-Referencing

### Internal Links

```markdown
See [API Stability](../coding/API_STABILITY.md) for details.
```

### Project References

```markdown
For project-specific details, see:
@~/Data1/Linux/Projects/Rust/Promps/.ai-context/ESSENTIAL.md
```

### External Links

```markdown
[Rust Documentation](https://doc.rust-lang.org/)
[Tauri Guide](https://tauri.app/v1/guides/)
```

---

## Version Control

### Commit Messages

**Pattern**: `docs: brief description`

**Examples**:
```
✅ docs: add API reference for parse_input
✅ docs: update getting started guide
✅ docs: fix typo in installation section

❌ update docs
❌ changes
❌ WIP
```

### File Status Labels

Add status to document frontmatter:

```markdown
**Status**: Draft
**Status**: Review
**Status**: Published
**Status**: Deprecated
```

---

## Common Patterns

### API Documentation

```markdown
## Function: parse_input

**Signature**:
```rust
pub fn parse_input(input: &str) -> Vec<PromptPart>
``` (close backticks)

**Parameters**:
- `input`: Raw input string to parse

**Returns**: Vector of parsed prompt parts

**Example**:
```rust
let parts = parse_input("_N:User を 作成");
assert_eq!(parts.len(), 3);
``` (close backticks)
```

### Tutorial Documentation

```markdown
## Step 1: Installation

Install Rust and Cargo:
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
``` (close backticks)

Verify installation:
```bash
cargo --version
``` (close backticks)

**Expected output**: `cargo 1.70.0 (...)` or similar
```

### Troubleshooting Documentation

```markdown
## Problem: Build fails with "cannot find function"

**Symptoms**:
- Cargo build fails
- Error message shows "cannot find function `xyz`"

**Cause**: Missing import or function not in scope

**Solution**:
```rust
use crate::module::xyz;  // Add this import
``` (close backticks)
```

---

## Documentation Workflow

```
1. Draft (draft/ directory)
   ↓
2. Review (peer review or self-review)
   ↓
3. Edit based on feedback
   ↓
4. Move to appropriate language directory (ja/ or en/)
   ↓
5. Commit with descriptive message
   ↓
6. Update status to "Published"
```

---

## Quality Checklist

Before marking documentation as "Published":

- [ ] All code examples tested and working
- [ ] No spelling or grammar errors
- [ ] Links verified (no broken links)
- [ ] Consistent formatting throughout
- [ ] Clear and concise language
- [ ] Appropriate detail level for target audience
- [ ] Cross-references accurate
- [ ] Status and date updated

---

**Follow these conventions to maintain consistent, high-quality documentation across all projects.**
