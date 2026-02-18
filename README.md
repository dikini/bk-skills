# BK (Build Kit): Structured Agent Skills for Software Development

A comprehensive system of agent skills for structured software development, combining rigorous process management with traceable implementation—currently focused on Rust with extensible architecture for other languages.

## Overview

BK provides AI agents with structured workflows for the entire software development lifecycle, from initial exploration through design, implementation, verification, and release. The system emphasizes **traceability** and **compliance** through spec-linked code markers and automated verification.

### Development Workflow

```
Explore → Research → Ideate → Design → Plan → Execute → Verify → Ship
```

This linear flow represents the typical path through the development lifecycle, though you may skip steps depending on your starting point and knowledge of the problem space.

## The Skills Ecosystem

### Process Skills: What & How to Build

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| **bk-explore** | Conversational exploration of ideas | Vague requirements, unclear scope |
| **bk-research** | Research problem space and solutions | Technology selection, novel problems |
| **bk-ideate** | Generate and evaluate approaches | Multiple viable paths, architecture decisions |
| **bk-design** | Create formal specifications | Ready to formalize design |
| **bk-plan** | Convert specs to implementation plans | Breaking down work into tasks |
| **bk-verify** | Audit spec compliance | Pre-commit, PR review, release readiness |
| **bk-ship** | Release preparation | Cutting releases, generating docs |

### Implementation Skills: Writing Code

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| **bk-execute** | Execute plans via subagents | Delegating implementation |
| **bk-implement-rust** | Generate idiomatic Rust code | Writing Rust modules/features |
| **bk-tdd** | Test-driven development | Requirements are clear |
| **bk-debug** | Systematic debugging | Investigating failures |
| **bk-verify-completion** | Pre-completion quality check | Before committing |
| **bk-finish-branch** | Complete and prepare PR | Work is done, ready to merge |
| **bk-worktree** | Create isolated workspaces | Parallel development |

## Quick Start

### New Project from Scratch

```
1. bk-explore → "I want to build a task scheduler"
   ↓
2. bk-research → Research Rust async scheduling libraries
   ↓
3. bk-design → Create COMP-SCHEDULER-001 spec
   ↓
4. bk-plan → Generate implementation plan with 6 tasks
   ↓
5. bk-execute --mode=sequential → Implement via subagents
   └─ Subagents: bk-implement-rust for each task
   ↓
6. bk-verify --scope=component/scheduler → Check compliance
   ↓
7. bk-finish-branch → Prepare PR with verification report
   ↓
8. bk-ship --version=0.1.0 → Release
```

### Well-Understood Feature

```
1. bk-design → Create spec for the feature
   ↓
2. bk-plan → Break into tasks
   ↓
3. bk-execute → Implement
   ↓
4. bk-verify → Compliance check
   ↓
5. bk-ship → Release
```

### Bug Fix

```
1. bk-debug → Identify root cause
   ↓
2. bk-tdd or bk-implement-rust → Write test, fix code
   ↓
3. bk-verify-completion → Quality check
   ↓
4. bk-finish-branch → PR
```

## Use Cases

### Use Case 1: Safety-Critical System Development

**Scenario**: Developing a medical device control system in Rust requiring FDA compliance.

**Workflow**:
1. **bk-explore** → Define safety requirements and constraints
2. **bk-research** → Evaluate Rust safety patterns and formal verification tools
3. **bk-design** → Create specs with hazard analysis (FMEA) linked to each requirement
4. **bk-plan** → Generate tasks with safety-critical acceptance criteria
5. **bk-implement-rust** → Code with traceability markers:
   ```rust
   /// SPEC-FR-3.1: Heart rate monitor must alarm within 5 seconds of threshold breach
   /// CONCERN-SAFETY-1: Patient safety critical path
   /// CONCERN-REL-1: Bounded operation (5 second SLA)
   pub async fn monitor_heart_rate(&self, reading: HeartRate) -> Result<AlarmState> {
       // Implementation with markers linking to safety requirements
   }
   ```
6. **bk-verify** → Generate compliance matrix showing:
   - 100% of safety requirements implemented
   - 100% of hazard mitigations tested
   - Traceability from code → spec → risk analysis

**Compliance Output**:
```
Compliance Matrix: docs/audit/safety-verification-2026-02-18.md

| Requirement | Implementation | Tests | Status |
|-------------|----------------|-------|--------|
| SPEC-FR-3.1 | monitor.rs:45 | test_alarm_within_5s | ✅ Full |
| SAFETY-001 | bounds.rs:12 | test_bounds_check | ✅ Full |

Spec Compliance: 100%
Critical Gaps: 0
Traceability: Complete
```

### Use Case 2: Auditable Enterprise Feature

**Scenario**: Adding audit logging to a financial services API, requiring complete traceability for compliance audits.

**Workflow**:
1. **bk-design** → Create spec with audit requirements:
   ```yaml
   concerns: [SEC, OBS, CONS]
   # SEC-001: All financial transactions logged
   # OBS-001: Structured logging with correlation IDs
   # CONS-001: Event ordering preserved
   ```
2. **bk-plan** → Tasks include "audit marker placement" as acceptance criteria
3. **bk-implement-rust** → Every financial operation marked:
   ```rust
   /// SPEC-FR-4: Record payment transaction
   /// CONCERN-SEC-1: Log for audit trail
   /// CONCERN-CONS-1: Timestamp ordering
   pub async fn record_payment(&self, payment: Payment) -> Result<TransactionId> {
       // CONCERN-OBS-1: Structured audit log
       self.audit_log.record(AuditEvent::PaymentReceived {
           correlation_id: self.context.correlation_id(),
           amount: payment.amount,
           timestamp: Utc::now(),
       });
       // ... implementation
   }
   ```
4. **bk-verify** → Proves all audit requirements are implemented and tested

**Audit Trail Output**:
```
Verification Report: docs/audit/payments-verification-2026-02-18.md

Audit Requirements Coverage:
- SEC-001 (Audit logging): ✅ 100% - 12/12 financial ops logged
- OBS-001 (Structured logs): ✅ 100% - All events JSON-schema compliant
- CONS-001 (Event ordering): ✅ 100% - Monotonic timestamp verified

Code-to-Spec Traceability:
- 47 SPEC markers found
- 0 orphaned requirements
- 0 unmarked financial operations
```

### Use Case 3: Regulatory Compliance (GDPR/Privacy)

**Scenario**: Implementing user data deletion ("right to be forgotten") in a data pipeline.

**Workflow**:
1. **bk-design** → Spec with privacy requirements:
   ```yaml
   concerns: [SEC, CONS, CAP]
   # SEC-002: Complete data deletion, no remnants
   # CONS-002: Deletion confirmed before response
   # CAP-002: Handle large deletion requests
   ```
2. **bk-implement-rust** → Markers for each deletion stage:
   ```rust
   /// SPEC-FR-7.1: Initiate user data deletion request
   /// CONCERN-SEC-2: GDPR Article 17 compliance
   pub async fn delete_user_data(&self, user_id: UserId) -> Result<DeletionConfirmation> {
       // CONCERN-CONS-2: Cascade deletion to all stores
       self.delete_from_primary(user_id).await?;
       self.delete_from_cache(user_id).await?;
       self.delete_from_analytics(user_id).await?;
       
       // CONCERN-CAP-2: Bounded operation
       Ok(DeletionConfirmation::new(user_id))
   }
   ```
3. **bk-verify** → Confirms complete deletion coverage across all data stores

### Use Case 4: Complex System with Cross-Cutting Concerns

**Scenario**: Building a distributed task scheduler with reliability, observability, and capacity concerns.

**Spec Structure**:
```yaml
# docs/specs/concern-registry.yaml
concerns:
  REL:
    name: Reliability
    requirements:
      - REL-001: Bounded retries with exponential backoff
      - REL-002: Circuit breaker pattern for downstream failures
  OBS:
    name: Observability
    requirements:
      - OBS-001: Distributed tracing for all operations
      - OBS-002: Metrics for queue depth and latency
  CAP:
    name: Capacity
    requirements:
      - CAP-001: Backpressure when queue depth exceeds threshold
      - CAP-002: Resource quotas per tenant
```

**Implementation**:
```rust
//! Distributed task scheduler
//! SPEC: COMP-SCHEDULER-001
//! CONCERNS: [REL, OBS, CAP]

/// SPEC-FR-1: Submit task to scheduler
/// CONCERN-REL-1: Retry with exponential backoff
/// CONCERN-CAP-1: Backpressure on queue limit
pub async fn submit(&self, task: Task) -> Result<TaskId> {
    // CONCERN-CAP-1: Check capacity before accepting
    if self.queue.depth() >= self.config.max_queue_depth {
        // CONCERN-OBS-2: Queue depth metric
        metrics::gauge!("scheduler.queue.depth", self.queue.depth() as f64);
        return Err(ScheduleError::CapacityExceeded);
    }
    
    // CONCERN-OBS-1: Trace span for distributed tracing
    let span = info_span!("scheduler.submit", task_id = ?task.id);
    
    // ... implementation with retry logic (REL-001)
}
```

**Verification**:
```
Compliance Matrix:

| Concern | Requirement | Implementation | Tests | Status |
|---------|-------------|----------------|-------|--------|
| REL | REL-001 (Retry) | retry.rs:45 | test_retry_bounds | ✅ Full |
| REL | REL-002 (Circuit) | circuit.rs:23 | test_circuit_open | ✅ Full |
| OBS | OBS-001 (Tracing) | submit:67 | test_trace_context | ✅ Full |
| OBS | OBS-002 (Metrics) | submit:34 | test_metrics_emitted | ✅ Full |
| CAP | CAP-001 (Backpressure) | submit:12 | test_backpressure | ✅ Full |
| CAP | CAP-002 (Quotas) | quota.rs:56 | test_quota_enforced | ✅ Full |

Concern Coverage: 100% (6/6)
```

## Traceability & Compliance System

The BK system provides enterprise-grade traceability through **traceability markers** embedded directly in source code.

### Marker Types

| Marker | Purpose | Example |
|--------|---------|---------|
| `SPEC-FR-N` | Links to functional requirement | `// SPEC-FR-3: Max concurrent runs` |
| `CONCERN-ID` | Links to cross-cutting concern | `// CONCERN-REL-1: Bounded retries` |
| `TASK-ID` | Links to implementation task | `// TASK: TS-004` |
| `TEST-ID` | Links test to acceptance criterion | `// TEST-ACC-2: Expired items evicted` |
| `INTERFACE-ID` | Links to interface spec | `// INTERFACE-CACHE-001` |

### Verification Workflow

```
Code → Markers → Compliance Matrix → Audit Report

1. bk-verify scans code for markers
2. Validates marker IDs against specs (detects hallucinations)
3. Builds compliance matrix
4. Identifies gaps: Missing | Untested | Orphaned
5. Generates audit report for compliance
```

### Hallucination Detection

The system validates all marker IDs against the spec repository:

```
[Validating markers]
  Scanning: src/scheduler.rs
    ✅ SPEC-SCHEDULER-001 (valid)
    ❌ HALLUCINATION: SPEC-SCHEDULER-999 (not found in docs/)
    ⚠️  Placeholder: SPEC-PENDING (allowed for prototypes)

Result: 1 hallucination found. Fix before proceeding.
```

This ensures traceability claims are always accurate.

## Project Structure

```
skills/
├── bk-bootstrap/           # Ecosystem router and workflow selection
├── bk-explore/             # Conversational exploration
├── bk-research/            # Problem space research
├── bk-ideate/              # Approach generation & evaluation
├── bk-design/              # Specification authoring
├── bk-plan/                # Implementation planning
├── bk-execute/             # Plan execution via subagents
│   └── references/
│       └── execution-modes.md
├── bk-verify/              # Compliance verification
│   └── references/
│       └── output-formats.md
├── bk-ship/                # Release preparation
│   └── references/
│       └── doc-templates.md
├── bk-implement-rust/      # Rust code generation
│   ├── references/
│   │   ├── code-templates.md
│   │   └── traceability-markers.md
│   └── scripts/
│       └── validate-markers.sh
├── bk-tdd/                 # Test-driven development
├── bk-debug/               # Systematic debugging
├── bk-verify-completion/   # Pre-completion quality check
├── bk-finish-branch/       # Branch completion
└── bk-worktree/            # Isolated workspace management
```

## Directory Conventions

When using BK skills, the following directory structure is established:

```
docs/
├── specs/
│   ├── system/
│   │   └── spec-map.md           # System architecture map
│   ├── component/                # Component specifications
│   ├── interface/                # Interface specifications
│   ├── cross-cutting/            # Concern specifications
│   └── concern-registry.yaml     # Cross-cutting concerns registry
├── plans/
│   └── YYYY-MM-DD-<feature>-implementation.md
├── audit/
│   └── <scope>-verification-<date>.md
├── research/
│   └── <topic>-<date>.md
├── ideation/
│   └── <problem>-<date>.md
├── explore/
│   └── <topic>-<date>.md
├── releases/
│   └── <version>/
│       ├── README.md
│       ├── CHANGELOG.md
│       └── verification-report.md
└── planning/
    └── roadmap-index.md
```

## Design Principles

1. **Process Before Implementation**: Always use exploration/design/planning skills before writing code

2. **All Implementation via Subagents**: `bk-execute` creates worktrees and dispatches subagents—never implement directly in main workspace

3. **Sequential by Default**: Use sequential execution unless tasks are truly independent

4. **Verify Incrementally**: Run `bk-verify-completion` after each task, `bk-verify` before shipping

5. **Traceability by Design**: Every requirement links to implementation links to tests

6. **YAGNI Ruthlessly**: Remove unnecessary features at every stage

## Skill Selection Guide

```
Do you know exactly what to build?
├── YES → Is it well-understood?
│         ├── YES → bk-plan → bk-execute
│         └── NO  → bk-design → bk-plan → bk-execute
└── NO  → Do you know the problem space?
          ├── YES → bk-ideate (evaluate approaches)
          └── NO  → bk-explore (flesh out idea)
```

## Getting Started

1. **Clone this repository** into your AI agent's skills directory
2. **Start with `bk-bootstrap`** to understand the workflow
3. **Try `bk-explore`** on a small idea to see the process in action
4. **Follow the Golden Path**: explore → design → plan → execute → verify → ship

## Extending to Other Languages

While currently focused on Rust (`bk-implement-rust`), the architecture supports additional languages:

```
skills/
├── bk-implement-rust/      # Existing
├── bk-implement-python/    # Create following same pattern
├── bk-implement-go/        # Create following same pattern
└── ...
```

Each language skill follows the same marker-based traceability system.

---

*BK (Build Kit) is designed for teams requiring rigorous software development practices, complete audit trails, and regulatory compliance. The system ensures that every line of code can be traced back to a requirement, every requirement is tested, and every release is verifiable.*
