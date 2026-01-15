---
paths:
  - "Nexus.Core/crates/nexus-workspace/**"
  - "Nexus.Services/crates/nexus-workspace-services/**"
---

# Workspace Management Rules

## Workspace Structure

A Nexus workspace contains:

```
workspace/
├── archetypes/           # Archetype definitions
├── specifications/       # Instance specifications
├── .nexus/
│   ├── worktrees/       # Change request worktrees
│   │   └── cr-<uuid>/   # Per-CR isolated directory
│   └── config.yaml      # Workspace configuration
└── .git/                # Main repository
```

## WorkspaceLoader

Loads archetype and specification files:

```rust
let loader = WorkspaceLoader::new(Path::new("./workspace"));
let archetypes = loader.load_archetypes()?;
let specifications = loader.load_specifications()?;
```

## Change Request Isolation

Each change request gets an isolated worktree:

```rust
// Create isolated environment
let worktree = workspace.create_worktree_for_cr(&cr_id)?;

// Make changes in isolation
worktree.add_specification(&spec)?;

// Merge back on approval
workspace.merge_worktree(&worktree)?;
```

## Workspace Services Layer

Higher-level operations in `nexus-workspace-services`:

| Service | Purpose |
|---------|---------|
| `SpecificationServiceGit` | Spec CRUD with git integration |
| `MergeOrchestrator` | CR merge workflow |
| `ValidationAction` | Validation trigger |
| `CommandDispatcher` | Route commands to aggregates |

## Event Mappers

Map domain events to workspace operations:

```rust
// When CR is approved, trigger merge
impl EventMapper for MergeOnApproval {
    fn handles(&self) -> &[&str] {
        &["ChangeRequestApproved"]
    }

    async fn map(&self, event: &DomainEvent) -> Result<Vec<WorkspaceAction>> {
        Ok(vec![WorkspaceAction::TriggerMerge(event.aggregate_id.clone())])
    }
}
```

## Do Not

- Access specification files directly (use WorkspaceLoader)
- Modify main branch without going through CR workflow
- Leave orphan worktrees after CR completion
- Mix worktree operations from different workspaces
