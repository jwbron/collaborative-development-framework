# Documentation Index

> A navigation hub for the collaborative development framework bootstrapping infrastructure.

## Bootstrapping Status

This repository is in **early bootstrapping phase**. The goal is to build out comprehensive tooling for human/LLM collaboration. Current state:

| Component | Status | Notes |
|-----------|--------|-------|
| Core philosophy | First draft | Three pillars defined |
| Technical foundations | Defined | Seven foundations identified |
| Technical specs | **Not started** | Next priority |

## Quick Start

| Goal | Start Here |
|------|------------|
| Understand the bootstrapping goal | [CLAUDE.md](../CLAUDE.md) |
| Understand the framework philosophy | [README.md](../README.md) |
| Review technical foundations | [Foundational Technical Requirements](Foundational-Technical-Requirements.md) |
| Find a specific document | [Document Catalog](#document-catalog) |
| Contribute to bootstrapping | [Contributing](#contributing) |

## Document Catalog

### Core Framework Documents

| Document | Description | Status |
|----------|-------------|--------|
| [README.md](../README.md) | Framework overview, three pillars, seven foundations | First draft |
| [CLAUDE.md](../CLAUDE.md) | LLM agent orientation for bootstrapping | First draft |
| [Pragmatic Guide](Pragmatic-Guide-Software-Engineering-Post-LLM-World.md) | Strategic umbrella connecting all three pillars | First draft |
| [Foundational Technical Requirements](Foundational-Technical-Requirements.md) | Seven technical foundations to be built | First draft |

### Pillar Documentation (First Drafts)

| Pillar | Document | Focus | Status |
|--------|----------|-------|--------|
| 1. Intentionality | [LLM-First Code Reviews](LLM-Assisted-Code-Review.md) | Where human attention should focus | First draft |
| 2. Rigor | [Human-Driven Development](Human-Driven-LLM-Navigated-Software-Development.md) | How humans and LLMs collaborate | First draft |
| 3. Care | [Radical Self-Improvement](Radical-Self-Improvement-for-LLMs.md) | How systems get better over time | First draft |

### Technical Specifications (To Be Created)

These specifications are the next priority for bootstrapping:

| Foundation | Spec Document | Status |
|------------|---------------|--------|
| Documentation Intelligence | `specs/documentation-intelligence.md` | Not started |
| Collaborative Planning | `specs/collaborative-planning.md` | Not started |
| Multi-Agent Framework | `specs/multi-agent-framework.md` | Not started |
| PR Reviewer System | `specs/pr-reviewer-system.md` | Not started |
| Codebase Analysis Engine | `specs/codebase-analysis.md` | Not started |
| Continual Self-Reflection | `specs/continual-self-reflection.md` | Not started |
| Security Architecture | `specs/security-architecture.md` | Not started |

## Framework Architecture

```
Collaborative Development Framework (Bootstrapping)
│
├── Philosophy (First Drafts - Can Change)
│   ├── Three Pillars
│   │   ├── LLM-First Code Reviews → Intentionality
│   │   ├── Human-Driven, LLM-Navigated Development → Rigor
│   │   └── Radical Self-Improvement → Care
│   │
│   └── Core Thesis: Leverage LLM strengths while preserving human strengths
│
├── Technical Foundations (Defined)
│   ├── Documentation Intelligence
│   ├── Collaborative Planning Framework
│   ├── Multi-Agent Framework
│   ├── PR Reviewer System
│   ├── Codebase Analysis Engine
│   ├── Continual Self-Reflection Framework
│   └── Security Architecture
│
└── Technical Specifications (Next Step)
    └── Specs that enable implementers to formulate detailed plans
```

## Topic Index

Find documentation by topic:

| Topic | Relevant Documents | Notes |
|-------|-------------------|-------|
| **Bootstrapping overview** | [CLAUDE.md](../CLAUDE.md), [README.md](../README.md) | Start here |
| **Agent coordination** | [Foundational Technical Requirements](Foundational-Technical-Requirements.md) | Multi-Agent Framework foundation |
| **Code review** | [LLM-First Code Reviews](LLM-Assisted-Code-Review.md) | Pillar 1, PR Reviewer System foundation |
| **Collaboration patterns** | [Human-Driven Development](Human-Driven-LLM-Navigated-Software-Development.md) | Pillar 2, Collaborative Planning foundation |
| **Documentation strategy** | This index, [CLAUDE.md](../CLAUDE.md) | Documentation Intelligence foundation |
| **Self-improvement** | [Radical Self-Improvement](Radical-Self-Improvement-for-LLMs.md) | Pillar 3, Continual Self-Reflection foundation |
| **Security** | [Foundational Technical Requirements](Foundational-Technical-Requirements.md) | Security Architecture foundation |
| **Technical requirements** | [Foundational Technical Requirements](Foundational-Technical-Requirements.md) | All seven foundations |

## For LLM Agents

### Navigation Strategy

When working in this repository:

1. **Read [CLAUDE.md](../CLAUDE.md) first** to understand the bootstrapping context
2. **Check this index** to locate relevant documentation
3. **Read linked documents** before contributing
4. **Note document status** - everything is a first draft, subject to change
5. **Focus on specs** - writing technical specifications is the current priority

### Document Status Legend

| Status | Meaning |
|--------|---------|
| **First draft** | Document exists, content is initial and may change significantly |
| **Not started** | Document needs to be created |
| **Draft** | Document in active development |
| **Stable** | Document content is relatively settled (none yet) |

### Creating Technical Specifications

Priority work is writing specs for the seven foundations. Each spec should:

1. **Define clear requirements** without over-specifying implementation
2. **Reference relevant philosophy** from pillar documents
3. **Identify dependencies** between foundations
4. **Enable implementation planning** - enough detail to formulate a detailed plan

## Contributing

### Bootstrapping Priorities

1. **Technical specifications** - Write specs for the seven foundations
2. **Philosophy refinement** - Improve first-draft documents based on experience
3. **Cross-referencing** - Ensure documents link to related content

### Adding New Documentation

1. Create the document in the appropriate location
2. Update this index with the new entry
3. Update [CLAUDE.md](../CLAUDE.md) if it affects the bootstrapping workflow
4. Update [README.md](../README.md) for major additions

### Document Standards

- Use clear, structured markdown
- Include tables for quick reference
- Cross-reference related content
- Mark status clearly
- Maintain dual-audience tone (directive for LLMs, guidance for humans)

---

*This index is part of the bootstrapping infrastructure. Keep it current as the framework evolves.*
