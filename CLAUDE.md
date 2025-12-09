# Collaborative Development Framework - LLM Instructions

## Purpose: Bootstrapping Infrastructure

This repository is **bootstrapping infrastructure** for building a comprehensive suite of human/LLM collaboration tooling. You are reading this because you may be one of the agents that helps build it.

**The goal**: Enable LLMs to formulate detailed implementation plans, then bootstrap themselves into the framework by building out all necessary tooling.

**What this repo IS:**
- A bootstrapping foundation for human-LLM collaboration tooling
- First-draft philosophy and framework definitions (subject to change)
- Definitions of technical foundations that need comprehensive specs

**What this repo IS NOT:**
- A codebase to run or test
- A finished product - everything here is a first draft
- Immutable specifications - these are starting points, not final answers

## Current State

We are starting from near zero. Here's what exists:

| Component | Status | Description |
|-----------|--------|-------------|
| Core philosophy | First draft | Three pillars (Intentionality, Rigor, Care) |
| Framework overview | First draft | [README.md](README.md) explains the approach |
| Technical foundations | Defined | Seven foundations identified in [Foundational-Technical-Requirements.md](docs/Foundational-Technical-Requirements.md) |
| Pillar documents | First draft | Initial exploration of each pillar |
| Technical specs | **Not yet written** | This is the next step |

### What Exists Now

**Philosophy documents** (first drafts, can change):
- [Pragmatic Guide](docs/Pragmatic-Guide-Software-Engineering-Post-LLM-World.md) - Strategic umbrella
- [LLM-First Code Reviews](docs/LLM-Assisted-Code-Review.md) - Pillar 1: Intentionality
- [Human-Driven Development](docs/Human-Driven-LLM-Navigated-Software-Development.md) - Pillar 2: Rigor
- [Radical Self-Improvement](docs/Radical-Self-Improvement-for-LLMs.md) - Pillar 3: Care
- [Foundational Technical Requirements](docs/Foundational-Technical-Requirements.md) - Seven technical foundations

### What's Next

**Comprehensive technical specifications** for each of the seven foundations:

1. **Documentation Intelligence** - Always-current, navigable documentation
2. **Collaborative Planning Framework** - Structured human-LLM collaboration
3. **Multi-Agent Framework** - Infrastructure for coordinating specialized LLM agents
4. **PR Reviewer System** - Automated review with specialized agents
5. **Codebase Analysis Engine** - Deep code understanding
6. **Continual Self-Reflection Framework** - Autonomous system improvement
7. **Security Architecture** - Safe autonomous agent operation

These specs will:
- Crisply define requirements and some implementation details
- Leave enough ambiguity for implementers to formulate their own detailed plans
- Enable future readers to bootstrap themselves into the framework

## Finding Documentation

**Start with:** [docs/index.md](docs/index.md) (if available) or [README.md](README.md)

| Need | Location |
|------|----------|
| Framework overview | [README.md](README.md) |
| Technical foundations | [docs/Foundational-Technical-Requirements.md](docs/Foundational-Technical-Requirements.md) |
| Pillar documents | See docs/ directory |

## The Framework at a Glance

### Three Pillars

| Pillar | Core Question | Guiding Value |
|--------|---------------|---------------|
| **LLM-First Code Reviews** | Where should human attention focus? | Intentionality |
| **Human-Driven, LLM-Navigated Development** | How should humans and LLMs collaborate? | Rigor |
| **Radical Self-Improvement** | How do systems get better over time? | Care |

### Core Thesis

Software engineering practices must evolve to leverage LLM strengths (exhaustive attention, pattern consistency, tireless execution) while preserving human strengths (strategic judgment, creative problem-solving, interpersonal collaboration).

## How to Work in This Repo

### Understanding the Bootstrapping Context

This repo is designed to be self-bootstrapping. The documents here should enable you to:

1. **Understand the philosophy** - Read the pillar documents to grasp the vision
2. **Identify what needs building** - Review the seven technical foundations
3. **Write specifications** - Create technical specs that enable implementation
4. **Build the tooling** - Implement the foundations based on specs

### Contributing to Bootstrapping

**Priority work**: Writing technical specifications for the seven foundations.

Each spec should:
- Define clear requirements without over-specifying implementation
- Include enough detail for an LLM to formulate a detailed implementation plan
- Reference relevant philosophy from the pillar documents
- Identify dependencies between foundations

**Document standards**:
- Use clear, structured markdown
- Include tables for quick reference
- Cross-reference related content
- Mark status clearly (draft, first iteration, stable)

### For Autonomous Agents

If you're an autonomous agent working on this repo:

1. **Read this file first** - Understand you're helping bootstrap the framework
2. **Read the philosophy** - Start with [README.md](README.md), then pillar documents
3. **Identify gaps** - What specs are missing? What needs clarification?
4. **Write specs, not implementations** - Focus on specifications that enable future implementation
5. **Keep PRs focused** - One logical change per PR
6. **Document your reasoning** - Explain why, not just what

## Key Principles

From the framework's philosophy:

- **Leverage LLM strengths**: exhaustive attention, pattern consistency, tireless execution
- **Preserve human strengths**: strategic judgment, creative problem-solving, interpersonal collaboration
- **The pillars are mutually reinforcing**: reviews inform self-improvement, self-improvement enhances reviews, the collaboration model governs both
- **Nothing is sacred**: These are first drafts. Challenge, improve, and adapt.

## Repository Structure

```
collaborative-development-framework/
├── README.md                    # Overview of pillars and foundations
├── CLAUDE.md                    # This file - LLM orientation
├── LICENSE                      # MIT License
└── docs/
    ├── index.md                 # Documentation navigation (if present)
    ├── Pragmatic-Guide-*.md     # Strategic umbrella document
    ├── Foundational-*.md        # Seven technical foundations defined
    ├── LLM-Assisted-*.md        # Pillar 1 document
    ├── Human-Driven-*.md        # Pillar 2 document
    ├── Radical-Self-*.md        # Pillar 3 document
    └── specs/                   # Technical specifications (to be created)
```

---

*This CLAUDE.md is itself part of the bootstrapping infrastructure. Improve it as needed.*
