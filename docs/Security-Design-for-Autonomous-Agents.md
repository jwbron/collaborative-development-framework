# Security Design for Autonomous LLM Agents

**Status:** Draft
**Author:** James Wiesebron, james-in-a-box
**Created:** December 2025
**Purpose:** Security architecture and design principles enabling safe autonomous LLM agent operation
**Guiding Value:** Safety by Design

---

> **Part of:** [Foundational Technical Requirements](Foundational-Technical-Requirements.md)

---

## Executive Summary

Autonomous LLM agents require a fundamentally different security model than traditional software. They combine the unpredictability of AI systems with the capability to take real-world actions—reading files, executing code, calling APIs, and modifying production systems.

**The core challenge:** How do we grant LLM agents enough autonomy to be useful while ensuring they cannot cause harm—whether through malice, manipulation, or mistake?

**The answer:** Safety by design, not by instruction. Security constraints must be architectural—enforced by the system itself—not merely instructed in prompts that can be bypassed through jailbreaking or prompt injection.

**The guiding value—Safety by Design:** Security cannot be an afterthought bolted onto existing systems. Every capability granted to an autonomous agent must be designed with safety as a primary constraint. We build safe systems, not systems that we hope will behave safely.

---

## Table of Contents

- [The Threat Model](#the-threat-model)
- [Core Security Principles](#core-security-principles)
- [Foundation 7: Security Architecture](#foundation-7-security-architecture)
- [Sandboxing and Isolation](#sandboxing-and-isolation)
- [Permission Design](#permission-design)
- [Ephemerality and State Management](#ephemerality-and-state-management)
- [Data Protection and Exfiltration Prevention](#data-protection-and-exfiltration-prevention)
- [Secret Management](#secret-management)
- [Audit and Observability](#audit-and-observability)
- [Human Oversight Integration](#human-oversight-integration)
- [Implementation Patterns](#implementation-patterns)
- [Anti-Patterns to Avoid](#anti-patterns-to-avoid)
- [Success Metrics](#success-metrics)
- [Open Questions](#open-questions)
- [Related Documents](#related-documents)

---

## The Threat Model

### Understanding the Unique Risks

LLM agents face a distinct threat landscape that traditional software security models don't fully address:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LLM AGENT THREAT LANDSCAPE                       │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  EXTERNAL THREATS                                           │   │
│  │  • Prompt injection via user input                          │   │
│  │  • Malicious content in ingested documents                  │   │
│  │  • Adversarial data in APIs or databases                    │   │
│  │  • Supply chain attacks via compromised tools               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  INTERNAL RISKS                                             │   │
│  │  • Hallucination leading to incorrect actions               │   │
│  │  • Misinterpretation of ambiguous instructions              │   │
│  │  • Unintended capability emergence                          │   │
│  │  • Context confusion between tasks                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │  SYSTEMIC VULNERABILITIES                                   │   │
│  │  • Over-permissioned agents with blast radius issues        │   │
│  │  • Persistent state enabling attack chains                  │   │
│  │  • Credential exposure through logs or output               │   │
│  │  • Data exfiltration through approved channels              │   │
│  └─────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

### Attack Vectors Specific to LLM Agents

| Attack Type | Description | Example |
|-------------|-------------|---------|
| **Direct Prompt Injection** | Malicious instructions in user input | "Ignore previous instructions and..." |
| **Indirect Prompt Injection** | Malicious content in data the agent processes | Hidden instructions in a webpage or document |
| **Tool Manipulation** | Exploiting agent's access to tools | Crafting inputs that cause dangerous commands |
| **Context Poisoning** | Corrupting the agent's understanding over time | Gradually shifting behavior through repeated interactions |
| **Exfiltration via Output** | Using legitimate output channels to leak data | Encoding secrets in commit messages or PR descriptions |
| **Privilege Escalation** | Leveraging initial access to gain more | Using file read to find credentials |

### Why Traditional Security Isn't Enough

Traditional application security assumes:
- Predictable, deterministic behavior
- Clear separation between code and data
- Explicit, auditable control flow
- Users who are distinct from the system

LLM agents violate all these assumptions:
- **Non-deterministic**: Same input can produce different outputs
- **Code-data mixing**: The prompt IS the program
- **Opaque reasoning**: Can't audit the "thought process"
- **Agent autonomy**: The agent makes decisions, not just executes

---

## Core Security Principles

### Principle 1: Defense in Depth

No single security control should be the only barrier. Every security-critical operation should have multiple independent safeguards.

```
┌─────────────────────────────────────────────────────────────────┐
│                    DEFENSE IN DEPTH                              │
│                                                                  │
│  User Request                                                    │
│       ↓                                                          │
│  ┌──────────────────┐                                            │
│  │ Input Validation │ ← Sanitize, validate, reject anomalies     │
│  └────────┬─────────┘                                            │
│           ↓                                                      │
│  ┌──────────────────┐                                            │
│  │ Permission Check │ ← Is this operation allowed?               │
│  └────────┬─────────┘                                            │
│           ↓                                                      │
│  ┌──────────────────┐                                            │
│  │ Sandbox Boundary │ ← Isolation from sensitive resources       │
│  └────────┬─────────┘                                            │
│           ↓                                                      │
│  ┌──────────────────┐                                            │
│  │ Action Execution │ ← Actually perform the operation           │
│  └────────┬─────────┘                                            │
│           ↓                                                      │
│  ┌──────────────────┐                                            │
│  │ Output Filtering │ ← Scan for secrets, PII, anomalies         │
│  └────────┬─────────┘                                            │
│           ↓                                                      │
│  ┌──────────────────┐                                            │
│  │ Audit Logging    │ ← Record everything for review             │
│  └──────────────────┘                                            │
└─────────────────────────────────────────────────────────────────┘
```

### Principle 2: Least Privilege

Agents should have the minimum permissions necessary for their task—no more, no less. Permissions should be:
- **Scoped**: Limited to specific resources
- **Time-bound**: Expire after task completion
- **Revocable**: Can be withdrawn immediately
- **Auditable**: Every grant and use is logged

### Principle 3: Assume Compromise

Design as if the agent will be compromised. The question isn't "if" but "when" an agent behaves unexpectedly. Systems should:
- **Limit blast radius**: Contain damage to smallest possible scope
- **Enable recovery**: Make it possible to undo harmful actions
- **Detect anomalies**: Notice when behavior deviates from expected
- **Fail safe**: Default to denying access when uncertain

### Principle 4: Explicit Over Implicit

Security boundaries must be explicit and architectural, not implied or instructed:

| Implicit (Weak) | Explicit (Strong) |
|-----------------|-------------------|
| "Don't access production" in prompt | Network isolation from production |
| "Keep secrets secure" instruction | Secret injection at runtime only |
| "Only read authorized files" | Filesystem permissions enforcement |
| "Don't exfiltrate data" | Egress filtering and monitoring |

### Principle 5: Reversibility

Autonomous actions should be reversible wherever possible:
- Prefer soft deletes over hard deletes
- Create backups before destructive operations
- Use feature flags for deployment changes
- Require human approval for irreversible actions

---

## Foundation 7: Security Architecture

**Purpose:** Provide the architectural foundations for secure autonomous agent operation.

**Strategic Intent:** Enable LLM agents to operate with meaningful autonomy while maintaining security guarantees that don't depend on the agent's cooperation.

### Position in the Framework

```
┌─────────────────────────────────────────────────────────────────────┐
│                    The Seven Foundations                            │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │              FOUNDATIONAL LAYER (Build First)                 │  │
│  │  ┌─────────────────────────┐  ┌─────────────────────────┐     │  │
│  │  │ 1. DOCUMENTATION        │  │ 2. COLLABORATIVE        │     │  │
│  │  │    INTELLIGENCE         │  │    PLANNING (CPF)       │     │  │
│  │  └───────────┬─────────────┘  └─────────────┬───────────┘     │  │
│  └──────────────┼──────────────────────────────┼─────────────────┘  │
│                 │                              │                    │
│                 └──────────────┬───────────────┘                    │
│                                ▼                                    │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │           3. MULTI-AGENT FRAMEWORK                            │  │
│  └───────┬──────────────┬──────────────┬─────────────────────────┘  │
│          │              │              │                            │
│          ▼              ▼              ▼                            │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐                     │
│  │ 4. PR      │  │ 5. CODE    │  │ 6. SELF-   │                     │
│  │ REVIEWER   │  │ ANALYSIS   │  │ REFLECTION │                     │
│  └────────────┘  └────────────┘  └────────────┘                     │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │           7. SECURITY ARCHITECTURE                            │  │
│  │              Cross-cutting concern enabling safe autonomy     │  │
│  │              Sandboxing | Permissions | Ephemerality | Audit  │  │
│  └───────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

### Key Capabilities

| Capability | What It Does | Why It Matters |
|------------|--------------|----------------|
| **Sandboxing** | Isolates agent execution environments | Limits blast radius of any compromise |
| **Permission System** | Fine-grained access control | Enforces least privilege |
| **Ephemeral Execution** | Stateless, disposable environments | Prevents persistent compromise |
| **Data Protection** | Prevents unauthorized data access/exfiltration | Protects sensitive information |
| **Secret Management** | Secure credential handling | Prevents credential exposure |
| **Audit System** | Comprehensive logging and monitoring | Enables detection and forensics |
| **Human Oversight** | Integration points for human review | Maintains accountability |

---

## Sandboxing and Isolation

### Container-Based Isolation

Every agent execution should occur within an isolated container environment:

```
┌─────────────────────────────────────────────────────────────────┐
│                    SANDBOXED EXECUTION                          │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                 HOST SYSTEM                              │   │
│  │                                                          │   │
│  │  ┌─────────────────────────────────────────────────┐    │   │
│  │  │            CONTAINER BOUNDARY                    │    │   │
│  │  │                                                  │    │   │
│  │  │  ┌────────────────────────────────────────┐     │    │   │
│  │  │  │         AGENT WORKSPACE                │     │    │   │
│  │  │  │                                        │     │    │   │
│  │  │  │  • Cloned repository (read/write)      │     │    │   │
│  │  │  │  • Temporary files                     │     │    │   │
│  │  │  │  • Build artifacts                     │     │    │   │
│  │  │  │                                        │     │    │   │
│  │  │  └────────────────────────────────────────┘     │    │   │
│  │  │                                                  │    │   │
│  │  │  RESTRICTED:                                     │    │   │
│  │  │  ✗ No SSH keys                                   │    │   │
│  │  │  ✗ No cloud credentials                          │    │   │
│  │  │  ✗ No production access                          │    │   │
│  │  │  ✗ No host filesystem access                     │    │   │
│  │  │  ✗ Limited network (HTTPS egress only)           │    │   │
│  │  │                                                  │    │   │
│  │  └─────────────────────────────────────────────────┘    │   │
│  │                                                          │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

### Network Isolation

| Network Access | Allowed | Rationale |
|---------------|---------|-----------|
| HTTPS outbound to approved hosts | ✓ | Required for APIs, package managers |
| HTTP outbound | ✗ | Force encrypted communication |
| SSH outbound | ✗ | Prevent unauthorized server access |
| Arbitrary TCP/UDP | ✗ | Minimize attack surface |
| Internal services | Scoped | Only what's needed for task |

### Filesystem Isolation

```
Filesystem Access Model:

/home/agent/
├── workspace/           # READ/WRITE - Agent's working directory
│   └── repo/            # Cloned repository
├── context/             # READ-ONLY - Provided context
│   ├── confluence/      # Documentation
│   └── jira/            # Task information
├── shared/              # READ/WRITE (persistent) - Cross-session data
│   ├── incoming/        # Messages from humans
│   └── outgoing/        # Messages to humans
└── secrets/             # NO ACCESS - Managed by runtime

BLOCKED:
/etc/                    # System configuration
/var/                    # System state
/root/                   # Root home
/home/other-users/       # Other users
```

### Resource Limits

Every agent container should have enforced resource limits:

| Resource | Limit | Rationale |
|----------|-------|-----------|
| CPU | 2-4 cores | Prevent resource exhaustion |
| Memory | 4-8 GB | Prevent OOM attacks on host |
| Disk | 10-20 GB | Prevent disk filling attacks |
| Process count | 100-200 | Prevent fork bombs |
| Execution time | Task-appropriate | Prevent infinite loops |
| Network bandwidth | Rate limited | Prevent data floods |

---

## Permission Design

### Capability-Based Security

Rather than role-based access, use capability tokens that grant specific permissions:

```
┌─────────────────────────────────────────────────────────────────┐
│                    CAPABILITY TOKEN                              │
│                                                                  │
│  {                                                               │
│    "id": "cap-abc123",                                           │
│    "granted_to": "agent-instance-xyz",                           │
│    "capabilities": [                                             │
│      {                                                           │
│        "resource": "github:jwbron/repo",                         │
│        "actions": ["read", "write", "create_pr"],                │
│        "constraints": {                                          │
│          "branches": ["feature/*", "fix/*"],                     │
│          "files": ["src/**", "tests/**"],                        │
│          "exclude": ["*.env", "secrets/**"]                      │
│        }                                                         │
│      }                                                           │
│    ],                                                            │
│    "expires_at": "2024-12-09T06:00:00Z",                         │
│    "revocable": true,                                            │
│    "audit_id": "audit-789"                                       │
│  }                                                               │
└─────────────────────────────────────────────────────────────────┘
```

### Permission Hierarchy

```
┌─────────────────────────────────────────────────────────────────┐
│                    PERMISSION LEVELS                             │
│                                                                  │
│  Level 0: READ-ONLY                                              │
│  ├── Read public documentation                                   │
│  ├── Query codebase (no modification)                            │
│  └── Generate suggestions (not applied)                          │
│                                                                  │
│  Level 1: DEVELOPMENT                                            │
│  ├── All Level 0 permissions                                     │
│  ├── Create/modify files in workspace                            │
│  ├── Run tests                                                   │
│  ├── Create branches                                             │
│  └── Open pull requests (human review required)                  │
│                                                                  │
│  Level 2: INTEGRATION                                            │
│  ├── All Level 1 permissions                                     │
│  ├── Push to protected branches (with approval)                  │
│  ├── Trigger CI/CD pipelines                                     │
│  └── Deploy to staging environments                              │
│                                                                  │
│  Level 3: PRODUCTION (Requires explicit human approval each time)│
│  ├── All Level 2 permissions                                     │
│  ├── Access production systems                                   │
│  ├── Modify production configuration                             │
│  └── Execute production deployments                              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Actions Requiring Human Approval

| Action Category | Examples | Approval Type |
|-----------------|----------|---------------|
| **Always Require** | Production deployment, secret rotation, user data access | Explicit per-action |
| **Batch Approval** | PR merges, branch pushes | Approval covers multiple actions |
| **Post-hoc Review** | Code changes, documentation updates | Review after action |
| **Autonomous** | Read operations, local testing | No approval needed |

---

## Ephemerality and State Management

### The Case for Ephemeral Execution

Persistent state creates security risks:
- **Accumulated compromise**: Attackers can build up access over time
- **Context confusion**: Old state can influence new tasks incorrectly
- **Credential exposure**: Secrets may persist in memory or logs
- **Audit complexity**: Harder to trace what happened when

### Ephemeral Execution Model

```
┌─────────────────────────────────────────────────────────────────┐
│                    EPHEMERAL EXECUTION                           │
│                                                                  │
│  Task Received                                                   │
│       ↓                                                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  FRESH CONTAINER CREATED                                  │   │
│  │  • Clean filesystem                                       │   │
│  │  • No previous state                                      │   │
│  │  • Fresh credentials (if needed)                          │   │
│  │  • New network identity                                   │   │
│  └──────────────────────────────────────────────────────────┘   │
│       ↓                                                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  TASK EXECUTION                                           │   │
│  │  • Agent performs work                                    │   │
│  │  • State exists only in container                         │   │
│  │  • Outputs written to designated locations                │   │
│  └──────────────────────────────────────────────────────────┘   │
│       ↓                                                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  CLEAN EXTRACTION                                         │   │
│  │  • Outputs collected (commits, PRs, reports)              │   │
│  │  • Audit logs captured                                    │   │
│  │  • Container destroyed                                    │   │
│  │  • All state eliminated                                   │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### What Persists vs. What Doesn't

| Persists | Doesn't Persist |
|----------|-----------------|
| Git commits (pushed) | Working directory |
| Pull requests | Environment variables |
| Audit logs | Process state |
| Task tracking (beads) | Memory contents |
| Notifications sent | Temporary files |
| Explicitly saved context | Credentials |

### Session Boundaries

Each "session" should be:
- **Bounded**: Clear start and end
- **Isolated**: No state leakage between sessions
- **Auditable**: Complete record of what happened
- **Recoverable**: Can reconstruct what the session did

---

## Data Protection and Exfiltration Prevention

### Data Classification

| Classification | Examples | Handling |
|---------------|----------|----------|
| **Public** | Open source code, public docs | No restrictions |
| **Internal** | Private repos, internal docs | Keep within organization |
| **Confidential** | User data, business logic | Strict access controls |
| **Restricted** | Credentials, PII, secrets | Never expose to agents |

### Exfiltration Prevention Strategies

```
┌─────────────────────────────────────────────────────────────────┐
│                 EXFILTRATION PREVENTION                          │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  INPUT FILTERING                                        │    │
│  │  • Scan ingested content for secrets                    │    │
│  │  • Detect and flag sensitive data patterns              │    │
│  │  • Quarantine suspicious content for review             │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  OUTPUT FILTERING                                       │    │
│  │  • Scan all outputs (commits, PRs, messages)            │    │
│  │  • Block content containing secrets                     │    │
│  │  • Detect encoded/obfuscated sensitive data             │    │
│  │  • Flag unusual output patterns                         │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  NETWORK CONTROLS                                       │    │
│  │  • Allowlist of permitted egress destinations           │    │
│  │  • Volume-based anomaly detection                       │    │
│  │  • Content inspection for sensitive patterns            │    │
│  │  • DNS query monitoring                                 │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │  BEHAVIORAL MONITORING                                  │    │
│  │  • Detect unusual file access patterns                  │    │
│  │  • Monitor for reconnaissance behavior                  │    │
│  │  • Alert on permission boundary probing                 │    │
│  │  • Track tool usage anomalies                           │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

### Output Sanitization

All agent outputs must be scanned before release:

| Check Type | What It Catches | Action on Detection |
|------------|-----------------|---------------------|
| Secret patterns | API keys, passwords, tokens | Block, alert |
| PII patterns | Emails, SSNs, credit cards | Block, alert |
| Encoded data | Base64 secrets, URL-encoded credentials | Decode and scan |
| Unusual volume | Abnormally large outputs | Review before release |
| File type check | Unexpected binary data | Block, investigate |

---

## Secret Management

### Secrets Never in Prompts

Secrets should NEVER appear in:
- Agent prompts or instructions
- Environment variables visible to agent code
- Files readable by the agent
- Logs or audit trails (redacted only)

### Runtime Secret Injection

```
┌─────────────────────────────────────────────────────────────────┐
│                 RUNTIME SECRET INJECTION                         │
│                                                                  │
│  Agent requests GitHub API access                                │
│       ↓                                                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  SECRET MANAGER (Outside Agent Boundary)                  │   │
│  │  1. Validates agent identity                              │   │
│  │  2. Checks permission for this secret                     │   │
│  │  3. Generates short-lived credential                      │   │
│  │  4. Injects into tool call (not visible to agent)         │   │
│  └──────────────────────────────────────────────────────────┘   │
│       ↓                                                          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  TOOL EXECUTION (Transparent to Agent)                    │   │
│  │  • Tool receives credential                               │   │
│  │  • Makes authenticated API call                           │   │
│  │  • Returns result to agent                                │   │
│  │  • Credential never visible in agent context              │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Credential Characteristics

| Property | Requirement |
|----------|-------------|
| Scope | Minimum necessary permissions |
| Lifetime | Short-lived (minutes to hours) |
| Rotation | Automatic, frequent |
| Revocation | Immediate capability |
| Audit | Every use logged |
| Visibility | Never in logs, prompts, or outputs |

---

## Audit and Observability

### Comprehensive Logging

Every agent action should generate an audit record:

```json
{
  "timestamp": "2024-12-09T04:35:00Z",
  "agent_id": "agent-xyz",
  "session_id": "session-abc",
  "task_id": "task-123",
  "action": "file_write",
  "resource": "/workspace/repo/src/feature.py",
  "details": {
    "bytes_written": 1234,
    "content_hash": "sha256:abcd..."
  },
  "permission_used": "cap-456",
  "outcome": "success",
  "duration_ms": 15
}
```

### What Must Be Logged

| Category | Events |
|----------|--------|
| **Authentication** | Session start/end, credential use |
| **Authorization** | Permission checks (granted and denied) |
| **Actions** | All tool invocations and results |
| **Data Access** | File reads, API calls, database queries |
| **Output** | All generated content (commits, PRs, messages) |
| **Anomalies** | Unusual patterns, errors, boundary probes |

### Real-Time Monitoring

```
┌─────────────────────────────────────────────────────────────────┐
│                 MONITORING DASHBOARD                             │
│                                                                  │
│  Active Sessions: 3         │  Alerts: 0 Critical, 2 Warning    │
│                             │                                    │
│  ┌─────────────────────────┐│┌──────────────────────────────┐   │
│  │  ACTIVITY STREAM        │││  ANOMALY DETECTION           │   │
│  │  ────────────────       │││  ──────────────────          │   │
│  │  04:35:01 file_read     │││  ⚠ Unusual file access       │   │
│  │  04:35:03 api_call      │││    pattern detected          │   │
│  │  04:35:05 git_commit    │││                              │   │
│  │  04:35:07 pr_create     │││  ⚠ Output volume 2x normal   │   │
│  │  ...                    │││                              │   │
│  └─────────────────────────┘│└──────────────────────────────┘   │
│                             │                                    │
│  ┌─────────────────────────┐│┌──────────────────────────────┐   │
│  │  PERMISSION USAGE       │││  RESOURCE CONSUMPTION        │   │
│  │  ──────────────────     │││  ────────────────────        │   │
│  │  github:read    45%     │││  CPU: ████░░░░░░ 40%         │   │
│  │  github:write   12%     │││  MEM: ██████░░░░ 60%         │   │
│  │  file:write     30%     │││  NET: ██░░░░░░░░ 20%         │   │
│  └─────────────────────────┘│└──────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

### Forensic Capability

The system must support after-the-fact investigation:
- **Session replay**: Reconstruct what an agent did
- **Timeline analysis**: Correlate events across time
- **Diff generation**: See exactly what changed
- **Attribution**: Track actions to specific agents/sessions

---

## Human Oversight Integration

### Escalation Triggers

Certain situations should automatically escalate to human review:

| Trigger | Severity | Response |
|---------|----------|----------|
| Permission denied repeatedly | Warning | Review agent behavior |
| Unusual data access pattern | Warning | Investigate before continuing |
| Secret-like content in output | Critical | Block output, investigate |
| Resource limit approached | Warning | Review task scope |
| Error rate spike | Warning | Pause and investigate |
| Explicit agent uncertainty | Normal | Human guidance requested |

### Human-in-the-Loop Patterns

```
┌─────────────────────────────────────────────────────────────────┐
│              HUMAN OVERSIGHT INTEGRATION                         │
│                                                                  │
│  Pre-Action Review          │  Post-Action Review                │
│  ──────────────────         │  ───────────────────               │
│  • Production deployments   │  • All code changes (PR review)    │
│  • Security-sensitive ops   │  • Documentation updates           │
│  • Infrastructure changes   │  • Configuration changes           │
│  • User data access         │  • Audit log review                │
│                             │                                    │
│  Async Notification         │  Emergency Override                │
│  ──────────────────         │  ──────────────────                │
│  • Task completion          │  • Kill switch (immediate stop)    │
│  • Anomaly detection        │  • Credential revocation           │
│  • Request for guidance     │  • Session termination             │
│  • Progress updates         │  • Rollback capability             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Override Capabilities

Humans must always be able to:
- **Stop**: Immediately halt agent execution
- **Revoke**: Remove any granted permission
- **Rollback**: Undo agent actions where possible
- **Investigate**: Access complete audit trail
- **Block**: Prevent specific actions or patterns

---

## Implementation Patterns

### Pattern 1: Sandboxed Task Execution

```yaml
# Container security configuration
security:
  container:
    image: "agent-sandbox:latest"
    readonly_root: true
    no_new_privileges: true
    drop_capabilities:
      - ALL
    add_capabilities: []

  network:
    egress:
      - "api.github.com:443"
      - "pypi.org:443"
      - "npmjs.org:443"
    ingress: none

  filesystem:
    mounts:
      - source: "/workspace"
        target: "/home/agent/workspace"
        mode: "rw"
      - source: "/context"
        target: "/home/agent/context"
        mode: "ro"

  resources:
    cpu_limit: "2"
    memory_limit: "4Gi"
    disk_limit: "10Gi"
```

### Pattern 2: Capability Token Grant

```python
def grant_capability(agent_id: str, task: Task) -> CapabilityToken:
    """Grant minimum necessary capabilities for a task."""

    capabilities = []

    # Analyze task to determine needed permissions
    if task.requires_code_changes:
        capabilities.append(Capability(
            resource=f"github:{task.repo}",
            actions=["read", "write", "create_pr"],
            constraints={
                "branches": [f"agent/{agent_id}/*"],
                "files": task.allowed_paths,
                "exclude": ["*.env", "secrets/**", ".credentials/**"]
            }
        ))

    # Create time-limited token
    return CapabilityToken(
        granted_to=agent_id,
        capabilities=capabilities,
        expires_at=datetime.now() + timedelta(hours=2),
        revocable=True,
        audit_id=create_audit_trail(agent_id, task)
    )
```

### Pattern 3: Output Sanitization

```python
def sanitize_output(content: str, context: SecurityContext) -> SanitizedOutput:
    """Scan and sanitize agent output before release."""

    issues = []

    # Check for secret patterns
    for pattern in SECRET_PATTERNS:
        if matches := pattern.findall(content):
            issues.append(SecurityIssue(
                severity="critical",
                type="potential_secret",
                matches=matches
            ))

    # Check for PII
    for pattern in PII_PATTERNS:
        if matches := pattern.findall(content):
            issues.append(SecurityIssue(
                severity="high",
                type="potential_pii",
                matches=matches
            ))

    # Check output volume
    if len(content) > context.expected_output_size * 2:
        issues.append(SecurityIssue(
            severity="warning",
            type="unusual_volume",
            details=f"Output {len(content)} bytes, expected ~{context.expected_output_size}"
        ))

    if any(i.severity == "critical" for i in issues):
        return SanitizedOutput(
            blocked=True,
            issues=issues,
            requires_review=True
        )

    return SanitizedOutput(
        blocked=False,
        content=content,
        issues=issues
    )
```

---

## Anti-Patterns to Avoid

### Anti-Pattern 1: Security by Instruction

**Problem:** Relying on prompts to enforce security

```
❌ "Never access production systems"
❌ "Don't reveal secrets in your output"
❌ "Only access files you're authorized to see"
```

**Why it fails:** Prompt instructions can be bypassed through jailbreaking, prompt injection, or simple model errors.

**Solution:** Architectural enforcement—if the network connection to production doesn't exist, the agent CAN'T access it regardless of instructions.

### Anti-Pattern 2: Over-Permissioning

**Problem:** Granting broad permissions "just in case"

```
❌ Admin access to entire GitHub organization
❌ Read access to all secrets
❌ Write access to production databases
```

**Why it fails:** Creates massive blast radius when (not if) something goes wrong.

**Solution:** Grant minimum necessary permissions, scoped to specific resources, with time limits.

### Anti-Pattern 3: Persistent Credentials

**Problem:** Long-lived credentials stored in agent environments

```
❌ Static API keys in environment variables
❌ SSH keys in agent filesystem
❌ Database passwords in configuration files
```

**Why it fails:** Credentials can be exfiltrated, leaked in logs, or accumulated by attackers.

**Solution:** Runtime credential injection with short-lived, scoped tokens that the agent never sees.

### Anti-Pattern 4: Trusting Agent Output

**Problem:** Using agent output without validation

```
❌ Auto-merging agent PRs without review
❌ Deploying agent-generated code directly
❌ Publishing agent-written content unreviewed
```

**Why it fails:** Agents can be manipulated, hallucinate, or make mistakes that require human judgment to catch.

**Solution:** Human review before any action with significant impact.

---

## Success Metrics

### Security Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Security incidents | 0 critical | Incident tracking |
| Secret exposure events | 0 | Automated scanning |
| Permission violations | Near 0 | Audit log analysis |
| Successful exfiltration | 0 | Output monitoring |
| Mean time to detect anomaly | < 5 minutes | Monitoring system |
| Mean time to revoke access | < 1 minute | Incident response |

### Operational Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Permission grant latency | < 100ms | System metrics |
| Container startup time | < 30s | System metrics |
| Audit log completeness | 100% | Log analysis |
| False positive rate (blocking) | < 1% | Review of blocked actions |

### Compliance Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Audit trail coverage | 100% | Log analysis |
| Human oversight compliance | 100% required reviews | Process tracking |
| Credential rotation | Per policy | Secret manager metrics |
| Security review coverage | 100% of new capabilities | Process tracking |

---

## Open Questions

### Technical Questions

1. **Sandbox escape prevention**: How do we stay ahead of container escape vulnerabilities?
2. **LLM-specific threats**: What novel attack vectors are unique to LLM agents?
3. **Performance vs. security**: How do we minimize security overhead while maintaining protection?
4. **Multi-agent security**: How do we secure agent-to-agent communication?

### Policy Questions

1. **Acceptable risk**: What level of autonomous action is acceptable without human review?
2. **Liability**: Who is accountable when an agent causes harm despite security measures?
3. **Transparency**: How much of our security architecture should be public?
4. **Evolution**: How do we update security measures as threats evolve?

### Integration Questions

1. **Existing systems**: How do we secure agents interacting with legacy systems?
2. **Third-party tools**: How do we vet and secure external tool integrations?
3. **Cross-organization**: How do we handle agents that need to work across security boundaries?

---

## Related Documents

| Document | Purpose |
|----------|---------|
| [Foundational Technical Requirements](Foundational-Technical-Requirements.md) | Technical foundations overview |
| [Pragmatic Guide](Pragmatic-Guide-Software-Engineering-Post-LLM-World.md) | Philosophy umbrella |
| [Human-Driven Development](Human-Driven-LLM-Navigated-Software-Development.md) | Human oversight principles |
| [Radical Self-Improvement](Radical-Self-Improvement-for-LLMs.md) | Self-monitoring and improvement |

---

Authored-by: jib
