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
