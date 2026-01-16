# Nexus Context Map

This document defines the bounded context relationships for the Nexus platform following Domain-Driven Design principles.

## Bounded Contexts Overview

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              NEXUS PLATFORM                                  │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────────────┐         ┌──────────────────┐                         │
│  │   SPECIFICATION  │◄───────►│ CHANGE MANAGEMENT│                         │
│  │     CONTEXT      │  async  │     CONTEXT      │                         │
│  │                  │ events  │                  │                         │
│  │  - Archetype     │         │  - ChangeRequest │                         │
│  │  - Specification │         │  - Review        │                         │
│  └────────┬─────────┘         └────────┬─────────┘                         │
│           │                            │                                    │
│           │ conformist                 │ customer-supplier                  │
│           ▼                            ▼                                    │
│  ┌──────────────────┐         ┌──────────────────┐                         │
│  │   VALIDATION     │◄────────│  COLLABORATION   │                         │
│  │    CONTEXT       │ triggers│    CONTEXT       │                         │
│  │                  │         │                  │                         │
│  │  - ValidationRun │         │  - Conversation  │                         │
│  │  - Problem       │         │  - AgentTask     │                         │
│  └────────┬─────────┘         └────────┬─────────┘                         │
│           │                            │                                    │
│           │ reacts_to                  │ customer-supplier                  │
│           │                            ▼                                    │
│           │                   ┌──────────────────┐                         │
│           └──────────────────►│      SAGA        │                         │
│                               │  ORCHESTRATION   │                         │
│                               │                  │                         │
│                               │  - SagaExecution │                         │
│                               │  - Compensation  │                         │
│                               └────────┬─────────┘                         │
│                                        │                                    │
│                                        │ triggers                           │
│                                        ▼                                    │
│  ┌──────────────────┐         ┌──────────────────┐                         │
│  │  CODE GENERATION │         │     POLICY       │                         │
│  │     CONTEXT      │         │    CONTEXT       │                         │
│  │                  │         │                  │                         │
│  │  - GeneratedCode │         │  - Policy        │                         │
│  │  - Artifact      │         │  - PolicyRule    │                         │
│  └──────────────────┘         └──────────────────┘                         │
│                                        │                                    │
│                                        │ open-host-service                  │
│                                        ▼                                    │
│                               ┌──────────────────┐                         │
│                               │   ALL CONTEXTS   │                         │
│                               │ (Query/Enforce)  │                         │
│                               └──────────────────┘                         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

## Context Definitions

### 1. Specification Context

**Crate:** `nexus-specification`

**Responsibility:** Manages the semantic specification layer - archetypes and their instances.

**Aggregates:**
- `Specification` - Formal definition of a domain concept
- `Archetype` - Meta-definition specifying schema and rules

**Ubiquitous Language:**
| Term | Definition |
|------|------------|
| Specification | A formal definition of a domain concept conforming to an archetype |
| Archetype | A meta-definition that specifies the schema and rules for specifications |
| QualifiedName | Unique identifier combining namespace and name (e.g., `com.example.Order`) |
| SemanticVersion | Version identifier following semver (major.minor.patch) |
| Draft | Initial editable state |
| Published | Available for use in the system |
| Deprecated | Marked for eventual removal |

---

### 2. Change Management Context

**Crate:** `nexus-change-management`

**Responsibility:** Manages the lifecycle of change requests through review and approval workflows.

**Aggregates:**
- `ChangeRequest` - Tracks proposed changes through workflow

**Ubiquitous Language:**
| Term | Definition |
|------|------------|
| ChangeRequest | A proposal to modify specifications |
| ProposedChange | A single modification within a change request |
| Review | Human assessment of a change request |
| Reviewer | Person assigned to review changes |
| Scope | Defines the boundary of changes |
| CascadeImpact | Analysis of downstream effects |
| Merged | Changes successfully applied to main |

**State Machine:**
```
Draft → Validating → UnderReview → Approved → Merged
                  ↘            ↘
                   Rejected     Cancelled
```

---

### 3. Validation Context

**Crate:** `nexus-validation`

**Responsibility:** Validates specifications against archetypes and business rules.

**Aggregates:**
- `ValidationRun` - Tracks validation execution

**Ubiquitous Language:**
| Term | Definition |
|------|------------|
| ValidationRun | An execution of the validation pipeline |
| ValidationProblem | An issue discovered during validation |
| ProblemSeverity | Error, Warning, or Info classification |
| ValidatorAgent | Component performing validation |
| Checkpoint | Progress marker in validation pipeline |

**Validation Pipeline:**
```
Structural → ArchetypeRules → Consistency → Z3 → Behavioral
```

---

### 4. Collaboration Context

**Crate:** `nexus-collaboration`

**Responsibility:** Manages conversations and agent interactions.

**Aggregates:**
- `Conversation` - Threaded user-agent interaction
- `AgentTask` - Unit of agent work

**Ubiquitous Language:**
| Term | Definition |
|------|------------|
| Conversation | A threaded interaction between users and agents |
| Message | A single communication within a conversation |
| Participant | User or agent in a conversation |
| AgentTask | A unit of agent work within a conversation |
| Invocation | A request for agent action |

---

### 5. Saga Orchestration Context

**Crate:** `nexus-saga`

**Responsibility:** Orchestrates long-running workflows across aggregate boundaries.

**Aggregates:**
- `SagaExecution` - Tracks saga progress

**Ubiquitous Language:**
| Term | Definition |
|------|------------|
| SagaDefinition | Blueprint for a workflow |
| SagaExecution | Running instance of a saga |
| SagaStep | Individual action in a saga |
| Compensation | Action that reverses a previous step on failure |
| Trigger | Event that initiates a saga |

**State Machine:**
```
Pending → Running → Completed
              ↓
         Compensating → Failed
```

---

### 6. Policy Context

**Crate:** `nexus-policies`

**Responsibility:** Manages reactive business rules and access control.

**Aggregates:**
- `Policy` - Collection of rules

**Ubiquitous Language:**
| Term | Definition |
|------|------------|
| Policy | A reactive business rule |
| PolicyRule | A specific rule within a policy |
| PolicyEffect | Allow or Deny outcome |
| PolicySubject | Who the policy applies to |
| PolicyResource | What the policy protects |
| PolicyCondition | When the policy applies |

---

### 7. Code Generation Context

**Crate:** `nexus-codegen`

**Responsibility:** Generates code artifacts from published specifications.

**Aggregates:**
- `GeneratedCode` - Tracks generation output

**Ubiquitous Language:**
| Term | Definition |
|------|------------|
| GeneratedCode | Output of the generation pipeline |
| Artifact | A generated file or package |
| TargetLanguage | Output language (Rust, TypeScript, etc.) |
| Template | Code generation template |
| Pipeline | Sequence of generation steps |

---

## Context Relationships

### Relationship Matrix

| Upstream | Downstream | Pattern | Integration |
|----------|------------|---------|-------------|
| Specification | Change Management | Customer-Supplier | Async Events |
| Change Management | Validation | Customer-Supplier | Async Events |
| Change Management | Saga Orchestration | Customer-Supplier | Async Events |
| Collaboration | Change Management | Partnership | Shared ID References |
| Validation | Change Management | Customer-Supplier | Async Events (results) |
| Specification | Code Generation | Customer-Supplier | Async Events |
| Policy | All Contexts | Open Host Service | Query/Enforcement |

### Relationship Patterns

#### Customer-Supplier
The upstream context (supplier) provides events that the downstream context (customer) consumes. The supplier sets the pace of change.

```
Specification ──events──► Change Management
                         (SpecificationPublished, ArchetypeUpdated)
```

#### Partnership
Both contexts evolve together with mutual dependencies. Changes require coordination.

```
Collaboration ◄──────► Change Management
              (conversation_id references)
```

#### Conformist
The downstream context conforms to the upstream model without translation.

```
Specification ──────► Validation
              (uses Specification types directly)
```

#### Open Host Service
A context exposes a well-defined protocol for others to consume.

```
Policy ──────► All Contexts
       (PolicyEnforcement API)
```

---

## Integration Events

### Cross-Context Events

| Event | Producer | Consumers | Purpose |
|-------|----------|-----------|---------|
| `SpecificationPublished` | Specification | Change Management, Code Generation | New spec available |
| `ArchetypeUpdated` | Specification | Validation | Re-validate conforming specs |
| `ChangeRequestSubmitted` | Change Management | Validation, Saga | Trigger validation |
| `ValidationCompleted` | Validation | Change Management | Update CR status |
| `ChangeRequestApproved` | Change Management | Saga | Trigger merge saga |
| `ConversationCompleted` | Collaboration | Saga | Cleanup workflow |

### Event Schema Contract

All integration events must include:

```rust
struct IntegrationEvent {
    event_id: Uuid,           // Unique event identifier
    event_type: String,       // Qualified event type
    aggregate_id: Uuid,       // Source aggregate
    aggregate_type: String,   // Source aggregate type
    occurred_at: DateTime,    // When event occurred
    correlation_id: Uuid,     // Request correlation
    causation_id: Uuid,       // Causing event/command
    payload: Value,           // Event-specific data
}
```

---

## Shared Kernel

The following types are shared across multiple contexts. Changes require coordination.

### Shared Value Objects

| Type | Owning Context | Consumers | Notes |
|------|----------------|-----------|-------|
| `QualifiedName` | Specification | Change Management, Validation | Immutable identifier |
| `SemanticVersion` | Specification | All | Version comparison |
| `SpecificationRef` | Specification | Change Management | Reference holder |

### Anti-Corruption Layer Requirements

When consuming shared types, contexts SHOULD define local translations:

```rust
// In Change Management context
mod acl {
    use nexus_specification::QualifiedName as SpecQualifiedName;

    pub struct TargetSpecification {
        pub qualified_name: String,  // Local representation
        pub version: String,
    }

    impl From<SpecQualifiedName> for TargetSpecification {
        fn from(qn: SpecQualifiedName) -> Self {
            Self {
                qualified_name: qn.to_string(),
                version: qn.version().to_string(),
            }
        }
    }
}
```

---

## Sagas

### Merge Saga

**Trigger:** `ChangeRequestApproved` event

**Participating Contexts:** Change Management, Collaboration, Saga

```
┌─────────────────────────────────────────────────────────────┐
│                        MERGE SAGA                           │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Step 1: CompleteConversation                              │
│  ├── Command: MarkConversationComplete                      │
│  └── Compensation: ResumeConversation                       │
│                                                             │
│  Step 2: MergeWorktree                                     │
│  ├── Command: GitMerge                                      │
│  └── Compensation: NONE (irreversible)                      │
│                                                             │
│  Step 3: DeleteWorktree                                    │
│  ├── Command: RemoveWorktree                                │
│  └── Compensation: NONE (cleanup)                           │
│                                                             │
│  Step 4: TransitionChangeRequest                           │
│  ├── Command: MarkMerged                                    │
│  └── Compensation: NONE (terminal)                          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Known Issues

### 1. Missing ACL for Shared Types

**Status:** Open

**Impact:** Tight coupling between contexts

**Recommendation:** Implement translation layers for cross-context type usage

### 2. Large ChangeRequest Aggregate

**Status:** Under Review

**Impact:** Potential contention at scale

**Recommendation:** Consider extracting `ReviewProcess` as separate aggregate

### 3. TOCTOU Race in ValidationAction

**Status:** Open

**Impact:** Possible duplicate validation runs

**Recommendation:** Use optimistic locking or distributed lock

---

## Governance

### Adding New Contexts

1. Define bounded context scope and ubiquitous language
2. Identify relationships with existing contexts
3. Update this context map
4. Define integration events
5. Implement ACL for any shared types

### Modifying Integration Events

1. Propose schema change with versioning strategy
2. Update all consuming contexts
3. Implement upcasting for backward compatibility
4. Update this context map

---

*Last Updated: 2026-01-16*
*Maintained by: Nexus Platform Team*
