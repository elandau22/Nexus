# Nexus

Specification-driven development platform using Domain-Driven Design (DDD) principles. Replaces unstructured natural language commands with a formal semantic model.

## Core Principles

- **Semantic Model First**: All solutions anchored in semantic model before implementation
- **Archetype-Driven**: Well-defined archetypes define schema and behavior of all components
- **Formal Verification**: Specifications must be algebraically verifiable before code generation
- **Contract-Driven**: Contracts enable parallel implementation
- **Event Sourcing**: All changes captured as events with full audit trail

## Repository Structure

| Repository | Purpose | Technology |
|------------|---------|------------|
| **Nexus.Core** | Foundation primitives, event sourcing, graph infrastructure | Rust |
| **Nexus.Services** | Domain services, agents, API gateway | Rust |
| **Nexus.Specification** | Archetype definitions and specification instances | YAML |
| **Nexus.Admin** | Web-based IDE for specification authoring | TypeScript/React |

## Quick Commands

```bash
# Rust (Core & Services)
cargo build --workspace
cargo test --workspace
cargo clippy --workspace

# Specification validation
python3 scripts/validate_archetypes.py

# Frontend (Admin)
cd Nexus.Admin && pnpm dev
```

## Key Patterns

### DDD Hierarchy
```
Domain -> Subdomain -> BoundedContext -> Aggregate -> Entity/ValueObject
```

### Message Types
- **Commands**: Intent to change state (imperative: CreateOrder)
- **Events**: State change that happened (past tense: OrderCreated)
- **Queries**: Read specifications (CQRS)
- **Policies**: Reactive rules (Event -> Condition -> Command)
- **Sagas**: Long-running workflows with compensation

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Archetypes | PascalCase | `Aggregate`, `Command` |
| Properties/Predicates | snake_case | `is_event_sourced`, `reacts_to` |
| Commands | Imperative verb | `CreateOrder`, `CancelOrder` |
| Events | Past tense | `OrderCreated`, `OrderCancelled` |

## Sub-Project Documentation

Each sub-project has its own CLAUDE.md with specific guidance:
- `Nexus.Core/CLAUDE.md` - Foundation crate patterns
- `Nexus.Services/CLAUDE.md` - Service layer and gateway rules
- `Nexus.Specification/CLAUDE.md` - Archetype authoring guidelines
- `Nexus.Admin/CLAUDE.md` - Frontend conventions

## Do Not

- Add business logic to gateway layer (route only)
- Use shell commands for git (use nexus-git library)
- Materialize transitive graph edges (derive via paths)
- Use inverse predicates in archetype edge declarations
- Skip validation after archetype changes

## Development Methodology

This project follows **Design-First Test-Driven Development**.

### Workflow Phases

```
📐 DESIGN → 🔴 RED → 🟢 GREEN → 📊 COVERAGE → 🔵 REFACTOR
     ↑                              │
     └──────────────────────────────┘
            (iterate if gaps)
```

1. **DESIGN** — Specify contracts and component boundaries
2. **RED** — Write failing tests that define behavior
3. **GREEN** — Write minimal implementation to pass tests
4. **COVERAGE** — Analyze gaps and iterate
5. **REFACTOR** — Improve quality, keep tests green

### Trigger Phrase

Use `/design-first-tdd` or include keywords like "implement", "add feature", "build" to activate the workflow.

### Testing Philosophy

- Tests describe BEHAVIOR, not implementation
- Given-When-Then format for clarity
- Unit tests for component contracts
- Integration tests for collaboration
- Behavioral tests for acceptance criteria

### Coverage Expectations

| Metric | Target |
|--------|--------|
| Line coverage | ≥90% |
| Branch coverage | ≥85% |
| Function coverage | ≥95% |
| Behavioral coverage | 100% |

### Contract Format

Components are defined by:
- **Inputs** (with validation rules)
- **Outputs** (success and failure variants)
- **Behaviors** (MUST/MUST NOT/SHOULD expectations)
- **Dependencies** (collaborating components)

### Phase Gates

| Phase | Gate |
|-------|------|
| DESIGN | User approval required |
| RED | All tests must fail |
| GREEN | All tests must pass |
| COVERAGE | Targets met (or iterate) |
| REFACTOR | Tests must remain green |

### TDD Agents

| Agent | Phase | Purpose |
|-------|-------|---------|
| `spec-designer` | DESIGN | Define contracts and boundaries |
| `tdd-test-writer` | RED | Write failing behavioral tests |
| `tdd-implementer` | GREEN | Minimal implementation |
| `coverage-analyst` | COVERAGE | Analyze gaps, recommend tests |
| `tdd-refactorer` | REFACTOR | Improve quality safely |
