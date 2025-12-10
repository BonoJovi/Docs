## Why System Development Fails: One Solution

## Introduction

“I used the same architecture that worked in the previous project, but this time it failed.”

Have you ever had this experience?

Through two projects (KakeiBon and Promps), I learned the hard way that **the appropriate architecture changes depending on the project's scale**. This article shares that insight and the criteria for making that judgment.

---

## Common Failure Patterns

### Pattern 1: Blindly Applying Past Successes

```
Project A: Huge success with layered architecture!
↓
Project B: Applied the same architecture
↓
Result: Frequent merge conflicts, slowed development...
```

### Pattern 2: Misunderstanding “Best Practices”

```
Book: “Layered Architecture is Optimal”
↓
Applied to all projects
↓
Result: Overkill for small projects, insufficient for large ones
```

---

## What Was the Problem?

The answer was simple.

**We were using the same architecture despite different scales**

This is like trying to drive a light truck on the highway or a large truck down a narrow alley.

---

## Discovery: Scale Determines Everything

What we noticed comparing two projects:

### Promps (Small Scale)

```
Purpose: 1 (DSL → Natural Language Conversion)
Features: 5-10
Layers: 5-10
Developers: 1-3

Adopted: Layered Architecture ✅
Result: Worked well
```

### KakeiBon (Large-Scale)

```
Purpose: Multiple (user management, transaction records, encryption, reporting...)
Features: 20+
Expected Layers: 15-20
Developers: 1 (to expand in the future)

Adopted: Modular Architecture ✅
If layered: Unmanageable ❌
```

### Scale and Architecture Correspondence Table

| Scale | Number of Layers | Optimal Architecture | Example |
|------|----------|-------------------|-----|
| **Small** | 5-10 | Layered | Promps |
| **Medium** | 10-20 | Hybrid | - |
| **Large** | 20+ | Modular/Microservices | KakeiBon |

---

## Conditions for Layered Architecture to Work

Actually, layered architecture has **application conditions**.

### ✅ Conditions for Success

- Single purpose
- Fewer than 10 layers
- Unidirectional dependencies
- Stable core layer

### ❌ Conditions Where It Doesn't Work

- Multiple purposes
- 15 or more layers
- Circular dependencies
- Frequent core changes

---

## Real-World Example: Success at Promps

Promps was designed to be **intentionally minimal**.

### Layer Structure

```
Phase N+1: File I/O (File Save/Load)
↓
Phase N: Logic Check (Syntax Validation)
↓
Phase 2: Particle Blocks (Particle Blocks)
↓
Phase 1: GUI (Blockly.js)
↓
Phase 0: Core Parsing (DSL → NL Conversion)
```

### Why It Worked

1. **Finite number of layers** (5-10)
2. **Clear responsibilities for each layer**
3. **Lower layers do not depend on higher layers**

As a result, the following were **automatically** achieved:

- ✅ Loose coupling (easy module separation)
- ✅ Test independence
- ✅ Rare merge conflicts

---

## Decision Criteria: 5 Questions

Before starting a new project, answer these 5 questions.

### Q1: How many objectives are there?

- **1** → Consider layered architecture
- **Multiple** → Consider modular architecture

### Q2: Can you list the number of layers?

- **Yes, fewer than 10** → Layered might work
- **No, or 10 or more** → Modular is safer

### Q3: Are dependencies unidirectional?

- **Mostly unidirectional** → Layered might work
- **Cyclic dependencies exist** → Modular is safer

### Q4: Is the core stable?

- **Yes** → API stability policy works
- **No** → Versioning is required

### Q5: How many developers are there?

- **1-3 people** → Layered is manageable
- **10+ people** → Consider carefully

---

## Alignment with the Unix Philosophy

Actually, this approach aligns with the Unix philosophy.

```
“Do one thing and do it well”
(Do one thing well)
```

Promps focused on **one thing: DSL transformation**, enabling a pure layered architecture.

Conversely, KakeiBon needed to handle **many things**, making a different approach appropriate.

---

## Architecture is a tool, not an absolute law

The most important lesson:

```
✅ Correct: Choose architecture based on scale
❌ Wrong: Apply one architecture to everything
```

Layered architecture is a great pattern, but **it is not a panacea**.

It's crucial to select the appropriate architecture based on the project's scale, purpose, and team composition.

---

## What this series aims to convey

This article provided an overview. We'll dive deeper in the following articles:

### 📝 Upcoming Articles (Planned)

1. **“How Loose Coupling Happened Naturally with Layered Architecture”**
- Why loose coupling is automatically achieved with layered architecture
- Real-world example at Promps

2. **“Criteria for Changing Architecture Based on Project Scale”**
- Detailed decision-making framework
- Common design pitfalls and countermeasures

3. **“Experimenting with a Git Strategy That Doesn't Delete Branches”**
- Persistent Feature Branch strategy
- Perfect alignment between layers and branches

---

## References

The content of this article is extracted from the development documentation of the OSS project “Promps”.

- **GitHub**: https://github.com/BonoJovi/Promps
- **Detailed Documentation**: [.ai-context/core/SCALE_AND_ARCHITECTURE.md](https://github.com/BonoJovi/Promps/blob/dev/.ai-context/core/SCALE_AND_ARCHITECTURE.md)
- **License**: MIT License (2025 Yoshihiro NAKAHARA)

The full documentation contains more detailed explanations from both AI and contributor perspectives.

---

## Summary

- ✅ Project scale determines the architecture
- ✅ Layered architecture is optimal for small-scale (5-10 layers)
- ✅ Modular architecture is appropriate for large-scale (15-20+ layers)
- ✅ Don't blindly apply successful patterns
- ✅ Decide using five questions before starting the project

In the next article, we'll explain in detail **why loose coupling naturally emerges** in layered architectures.

Stay tuned!

---

## About the Writing Process

This article was created by translating the author's (Yoshihiro NAKAHARA) design philosophy and practical experience into language through sessions with AI (Claude), then transcribing it into a manuscript.

- **Organizing Thoughts**: Explicitly articulating tacit knowledge gained through multiple projects (KakeiBon, Promps) via dialogue
- **Manuscript Drafting**: Claude handled structuring and writing
- **Content Verification**: The author confirmed technical accuracy and nuance

All design decisions and technical insights are based on the author's practical experience.
