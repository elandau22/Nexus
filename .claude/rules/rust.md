---
paths:
  - "Nexus.Core/**/*.rs"
  - "Nexus.Services/**/*.rs"
---

# Rust Conventions

Shared conventions for all Rust code in Nexus.Core and Nexus.Services.

## Error Handling

- Use `thiserror` for defining error types
- Prefer `anyhow::Result` for application code, concrete errors for library boundaries
- Always provide context with `.context()` or `.with_context()`
- Fail fast at system boundaries with early validation

## Async Patterns

- Use Tokio runtime for all async code
- Prefer `async fn` over returning `impl Future`
- Use `tokio::spawn` for concurrent tasks
- Always handle `JoinHandle` results

## Type System

- Prefer newtype wrappers for domain types (e.g., `struct OrderId(String)`)
- Use `#[derive(Debug, Clone, PartialEq)]` on value types
- Implement `Display` for types that will be user-facing
- Use `Cow<'_, str>` when ownership is uncertain

## Testing

- Use `#[cfg(test)]` modules in the same file
- Use `testcontainers` for integration tests requiring databases
- Name tests descriptively: `test_create_order_fails_when_customer_not_found`
- Use `proptest` for property-based testing where appropriate

## Dependencies

- Never add dependencies without checking existing workspace deps first
- Prefer workspace-level dependency management
- Avoid `unsafe` unless absolutely necessary and well-documented

## Do Not

- Use `unwrap()` or `expect()` in production code paths
- Use `std::process::Command` - delegate to service layer
- Block async context with synchronous operations
- Clone Arc unnecessarily - pass references where possible
