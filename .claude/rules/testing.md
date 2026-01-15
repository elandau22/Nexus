---
paths:
  - "Nexus.Core/**/tests/**"
  - "Nexus.Services/**/tests/**"
  - "**/*_test.rs"
  - "**/*.test.ts"
  - "**/*.test.tsx"
---

# Testing Rules

## Test Categories

| Category | Location | Purpose |
|----------|----------|---------|
| Unit | `src/*.rs` with `#[cfg(test)]` | Single module behavior |
| Integration | `tests/*.rs` | Cross-crate interaction |
| BDD | `tests/*_bdd.rs` | User scenario validation |
| E2E | `tests/e2e_*.rs` | Full pipeline testing |

## BDD Test Pattern

BDD tests follow Given-When-Then structure in comments:

```rust
//! BDD Test: Change Request Lifecycle
//!
//! ## Scenario: Merge approved change request
//!
//! ### Given
//! - A conversation with specifications
//! - A submitted change request
//!
//! ### When
//! - CR is approved and merged
//!
//! ### Then
//! - Git merge completes
//! - Worktree is cleaned up
//! - Conversation is archived
```

## TestWorld Pattern

Integration tests use a `TestWorld` struct:

```rust
struct TestWorld {
    client: Client,
    base_url: String,
    session_id: Option<String>,
    collected_events: Vec<ReceivedEvent>,
}

impl Default for TestWorld {
    fn default() -> Self {
        Self {
            client: Client::new(),
            base_url: env::var("GATEWAY_URL")
                .unwrap_or_else(|_| "http://localhost:8080".into()),
            // ...
        }
    }
}
```

## Database Testing

Use testcontainers for database tests:

```rust
use testcontainers::{clients::Cli, images::postgres::Postgres};

#[tokio::test]
async fn test_with_postgres() {
    let docker = Cli::default();
    let postgres = docker.run(Postgres::default());
    let port = postgres.get_host_port_ipv4(5432);
    // ... test with ephemeral DB
}
```

## SurrealDB Testing

Use in-memory mode:

```rust
let storage = SurrealDbStorage::in_memory().await?;
```

## Running Tests

```bash
# Unit tests
cargo test -p nexus-validation

# Integration tests (requires gateway)
GATEWAY_URL=http://localhost:8080 cargo test -p nexus-gateway --test '*_integration'

# BDD tests
cargo test -p nexus-gateway --test '*_bdd' -- --nocapture
```

## Do Not

- Use real databases in unit tests
- Skip `--nocapture` for BDD tests (need output for debugging)
- Create tests that depend on execution order
- Leave test resources uncleaned (use Drop or cleanup hooks)
