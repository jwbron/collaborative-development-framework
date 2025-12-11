# How to not code, well

A pragmatic philosophy and framework for software engineering in the post-LLM era.

## Purpose

This repository provides a **collection of documents designed to help technical organizations bootstrap themselves into pragmatic post-LLM software development workflows**. Rather than prescribing a single approach, it offers strategic guidance, implementation patterns, and decision frameworks that teams can adapt to their specific context.

**These documents serve two distinct audiences: humans and LLMs.**

For **LLMs**, these documents are **directives**. They are written in a prescriptive, dogmatic style to provide clear, unambiguous instructions that LLM agents can follow consistently. This intentional framing helps LLMs operate effectively within defined boundaries.

For **humans**, these documents are **guidance**. Despite their authoritative tone, they should be understood as a flexible and living collection of best practices—not immutable rules. Human readers are encouraged to interpret, adapt, and challenge these recommendations based on their specific context, experience, and evolving understanding of what works.

## What This Is Not

This repository is **not an implementation**. It contains no code and does not prescribe how to build your tooling. Instead, it is both a guide and an exercise.

The specifications here have intentional gaps. These gaps are meant to be filled in by you, using the frameworks established in this repository, to fit your existing workflows or to create new ones. The process of resolving these gaps—applying the Collaborative Planning Framework to your own context, making decisions about open questions, adapting patterns to your stack—is where the real value emerges.

If you're looking for a turnkey solution, this isn't it. If you're looking for a structured way to think about human-LLM collaboration that you can adapt to your needs, read on.

## Overview

The framework is built on three mutually reinforcing pillars:

| Pillar | Core Question | Guiding Value |
|--------|---------------|---------------|
| **LLM-First Code Reviews** | Where should human attention focus? | Intentionality |
| **Human-Driven, LLM-Navigated Development** | How should humans and LLMs collaborate? | Rigor |
| **Radical Self-Improvement** | How do systems get better over time? | Care |

## Core Thesis

Software engineering practices must evolve to leverage LLM strengths (exhaustive attention, pattern consistency, tireless execution) while preserving human strengths (strategic judgment, creative problem-solving, interpersonal collaboration).

## Key Documents

- **[Pragmatic Guide](docs/Pragmatic-Guide-Software-Engineering-Post-LLM-World.md)** - Strategic umbrella connecting all three pillars
- **[Foundational Technical Requirements](docs/Foundational-Technical-Requirements.md)** - The seven technical foundations for implementation
- **[LLM-First Code Reviews](docs/LLM-Assisted-Code-Review.md)** - Pillar 1: Intentionality in review
- **[Human-Driven Development](docs/Human-Driven-LLM-Navigated-Software-Development.md)** - Pillar 2: Rigor in collaboration
- **[Radical Self-Improvement](docs/Radical-Self-Improvement-for-LLMs.md)** - Pillar 3: Care in continuous improvement

## The Seven Technical Foundations

1. **Documentation Intelligence** - Always-current, navigable documentation
2. **Collaborative Planning Framework** - Structured human-LLM collaboration
3. **Multi-Agent Framework** - Infrastructure for coordinating specialized LLM agents
4. **PR Reviewer System** - Automated review with specialized agents
5. **Codebase Analysis Engine** - Deep code understanding
6. **Continual Self-Reflection Framework** - Autonomous system improvement
7. **Security Architecture** - Safe autonomous agent operation through sandboxing, permissions, and audit

## Philosophy

> Each pillar supports the weight of the whole idea.

The three pillars are mutually reinforcing:
- Reviews inform self-improvement
- Self-improvement enhances reviews
- The collaboration model governs both

## A Living Document

**This repository is a living document.** It is expected to change and adapt significantly over time as we learn, experiment, and refine our understanding of human-LLM collaboration.

**Nothing in this repository should be considered sacred.** The ideas, patterns, and recommendations here represent our current best thinking, not immutable truths. We encourage:

- **Challenging assumptions** - If something doesn't work for your context, question it
- **Proposing alternatives** - Better approaches are always welcome
- **Adapting freely** - Take what works, modify what doesn't, ignore what's irrelevant
- **Contributing back** - Share your learnings so others can benefit

The field of LLM-assisted software development is evolving rapidly. What works today may be obsolete tomorrow, and what seems impractical now may become essential. This framework will evolve alongside that reality.

## Status

**Active Development** - This framework is under active development and will continue to evolve.

**Foundation Complete** - The core philosophy and framework definitions have reached their intended scope. Iteration on existing content will continue, but the foundations are established.

**Fork and Build** - Organizations and individuals should fork this repository, then continue building it out for their specific context. The foundations are designed to be adapted, extended, and challenged as you discover what works for your team.

## License

MIT License - see [LICENSE](LICENSE) for details.
