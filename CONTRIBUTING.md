# Contributing Guidelines

Thank you for your interest in contributing to this documentation repository.
This guide explains how to contribute and what to keep in mind.

## Table of Contents

- [Types of Contributions](#types-of-contributions)
- [Before You Begin](#before-you-begin)
- [Documentation Structure](#documentation-structure)
- [Contribution Process](#contribution-process)
- [Style Guide](#style-guide)
- [Review Process](#review-process)

## Types of Contributions

We welcome the following types of contributions:

### 1. Error Corrections

- Fixing typos and spelling errors
- Correcting technical errors
- Fixing broken links
- Fixing code samples

**Process**: Create an issue or submit a Pull Request directly.

### 2. Improving Existing Documentation

- Adding or expanding explanations
- Adding examples
- Adding diagrams or illustrations
- Improving structure
- Enhancing readability

**Process**: First propose via issue, then submit a Pull Request after agreement.

### 3. Creating New Documentation

- Documentation on new topics
- Tutorials
- Guides
- Reference materials

**Process**: Propose via issue, discuss the content, then submit a Pull Request.

### 4. Translation

- Japanese to English translation
- English to Japanese translation

**Process**: Declare your translation plan in an issue, then submit a Pull Request.

## Before You Begin

### Required Checks

1. **Check Existing Issues**
   - Verify that the same problem or proposal hasn't already been reported
   - Use the search function to avoid duplicates

2. **Verify Latest Version**
   - Check if the issue has been fixed in the latest main branch
   - Ensure you're working with the latest documentation

3. **Clear Scope**
   - Focus on one topic per Pull Request
   - Split multiple different changes into separate Pull Requests

## Documentation Structure

```
~/Data1/Docs/
├── ja/                    # Japanese documentation
├── en/                    # English documentation
├── draft/                 # Drafts and work in progress
├── .ai-context/           # AI context files
└── .github/               # GitHub configuration
    ├── ISSUE_TEMPLATE/    # Issue templates
    └── pull_request_template.md
```

### Directory Usage

- **`ja/`**: Completed Japanese documentation
- **`en/`**: Completed English documentation
- **`draft/`**: Documents in progress or awaiting review

## Contribution Process

### 1. Fork the Repository

```bash
# Click the Fork button on GitHub
# Clone your forked repository
git clone https://github.com/YOUR_USERNAME/Docs.git
cd Docs
```

### 2. Create a Branch

```bash
# Create a new branch from main
git checkout -b fix/typo-in-rust-intro
# or
git checkout -b feature/add-error-handling-guide
```

**Branch Naming Convention**:
- `fix/` - Error corrections
- `feature/` - New documentation
- `improvement/` - Improvements to existing documentation
- `translation/` - Translations

### 3. Make Changes

#### For New Documentation

1. Start working in the `draft/` directory
2. Move to the appropriate language directory when complete

```bash
# Create draft
touch draft/new-document.md

# Move when complete
git mv draft/new-document.md en/technical-articles/
```

#### For Existing Documentation

Edit the relevant file directly.

### 4. Commit

```bash
git add .
git commit -m "docs: Fix typo in Rust introduction"
```

**Commit Message Convention**:
- `docs:` - Adding or changing documentation
- `fix:` - Error correction
- `translation:` - Translation
- `meta:` - Metadata or configuration changes

### 5. Push and Create Pull Request

```bash
git push origin fix/typo-in-rust-intro
```

Create a Pull Request on GitHub.

## Style Guide

### Markdown

- Use heading levels correctly (only one H1)
- Specify language for code blocks
- Use list hierarchies correctly
- Use line breaks and spaces appropriately

### Japanese

- Use です・ます form consistently
- Explain technical terms on first use
- Use 「、」「。」for punctuation
- Don't separate words with half-width spaces (in Japanese text)

### English

- Use clear, concise language
- Define technical terms on first use
- Use active voice when possible
- Follow standard punctuation rules

### Code Samples

```rust
// ✅ Good: Add explanatory comments
fn main() {
    let x = 5; // immutable variable
    println!("x = {}", x);
}

// ❌ Bad: No explanation
fn main() {
    let x = 5;
    println!("x = {}", x);
}
```

- Include working code
- Add comments to important parts
- Use correct indentation
- Explicitly mark examples that will error

### Links

```markdown
<!-- ✅ Use relative paths -->
See [here](../basics/intro.md) for details

<!-- ❌ Avoid absolute paths -->
See [here](/home/user/Docs/en/basics/intro.md) for details
```

## Review Process

### Pull Request Checklist

Reviewers will check the following:

1. **Content Accuracy**
   - No technical errors
   - Information is up to date
   - Code samples work

2. **Quality**
   - Clear explanations
   - Appropriate examples included
   - Logical structure

3. **Style**
   - Follows style guide
   - Correct markdown
   - Consistency

### After Review

- Respond to comments
- Make necessary corrections
- Request re-review after corrections

### Merging

Once the review is approved and all checks pass, maintainers will merge the Pull Request.

## Questions and Support

- **Issues**: Use issues for questions and proposals
- **Discussions**: Use Discussions for general discussions (if configured)

## License

Contributions to this repository are provided under the same license as the repository.

---

**Thank you for your contribution!**

Your contributions help create better documentation.
