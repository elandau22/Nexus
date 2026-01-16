---
name: design-first-tdd
description: Enforces Design-First Test-Driven Development workflow. Triggers on "implement", "add feature", "build", "create functionality". Does NOT trigger for bug fixes, documentation, or configuration changes.
---

# Design-First TDD Workflow

You are now enforcing the Design-First Test-Driven Development workflow. Every new feature MUST follow this sequence. Phases cannot be skipped.

## Workflow Sequence

```
📐 DESIGN → 🔴 RED → 🟢 GREEN → 📊 COVERAGE → 🔵 REFACTOR
     ↑                              │
     └──────────────────────────────┘
            (iterate if gaps)
```

## Mandatory Phase Sequence

### Phase 1: DESIGN 📐

**Goal**: Specify contracts and component boundaries.

Invoke the `spec-designer` agent with:
- Feature requirements from user request
- Existing system context

**Outputs**:
- Requirements specification
- Component contracts (inputs, outputs, errors, behaviors)
- Dependency diagram
- Implementation plan

**Gate**: User must approve design before proceeding to RED phase.

**Transition**: Ask user "Does this design capture your requirements? May I proceed to writing tests?"

---

### Phase 2: RED 🔴

**Goal**: Write failing tests that define expected behavior.

Invoke the `tdd-test-writer` agent with:
- Approved contracts from DESIGN phase
- Behavioral specifications

**Outputs**:
- Test specifications (Given-When-Then)
- Test files created
- All tests failing (with output proof)

**Gate**: All tests must FAIL before proceeding to GREEN phase.

**Transition**: Confirm "All {n} tests are failing as expected. Ready to implement?"

---

### Phase 3: GREEN 🟢

**Goal**: Write minimal implementation to make tests pass.

Invoke the `tdd-implementer` agent with:
- Failing tests from RED phase
- Contracts from DESIGN phase

**Outputs**:
- Implementation files
- All tests passing (with output proof)

**Gate**: All tests must PASS before proceeding to COVERAGE phase.

**Transition**: Confirm "All {n} tests are now passing. Running coverage analysis..."

---

### Phase 4: COVERAGE 📊

**Goal**: Analyze coverage and identify gaps.

Invoke the `coverage-analyst` agent with:
- Implementation files
- Test files
- Coverage report

**Outputs**:
- Coverage metrics (line, branch, function)
- Gap analysis
- Recommended additional tests (if gaps exist)

**Gate**:
- If coverage < targets OR behavioral gaps exist → Return to RED phase
- If coverage meets targets → Proceed to REFACTOR phase

**Transition (iteration)**: "Coverage gaps found. Returning to RED phase to add {n} additional tests."

**Transition (proceed)**: "Coverage targets met. Proceeding to refactor phase."

---

### Phase 5: REFACTOR 🔵

**Goal**: Improve code quality while keeping tests green.

Invoke the `tdd-refactorer` agent with:
- Implementation files
- Test files

**Outputs**:
- Refactoring changes (or "no refactoring needed" with rationale)
- Tests still passing (with output proof)

**Gate**: Cycle complete when refactor returns.

**Transition**: "TDD cycle complete. Feature implementation finished."

---

## Phase Violation Rules

**NEVER**:
- Write implementation before contracts are approved
- Write implementation before tests exist and fail
- Proceed to GREEN without RED failing
- Skip coverage analysis
- Add functionality during REFACTOR
- Start new feature before completing current cycle

**ALWAYS**:
- Get user approval after DESIGN phase
- Verify test failure before implementation
- Verify test success before coverage
- Iterate on coverage gaps
- Complete the full cycle

---

## Agent Context Isolation

Each agent operates with isolated context to prevent "cheating":

| Agent | Sees | Does NOT See |
|-------|------|--------------|
| spec-designer | Requirements, architecture | Implementation ideas |
| tdd-test-writer | Contracts, behavioral specs | Implementation plans |
| tdd-implementer | Failing tests, contracts | How tests were designed |
| coverage-analyst | Code, tests, coverage | Implementation rationale |
| tdd-refactorer | Passing code, tests | Original implementation |

---

## Coverage Targets

| Metric | Target |
|--------|--------|
| Line coverage | ≥90% |
| Branch coverage | ≥85% |
| Function coverage | ≥95% |
| Behavioral coverage | 100% |

---

## Trigger Keywords

This workflow activates when user requests include:
- "implement"
- "add feature"
- "build"
- "create functionality"
- "new feature"
- "develop"

This workflow does NOT activate for:
- Bug fixes (unless implementing new behavior)
- Documentation changes
- Configuration changes
- Refactoring existing code
- Research/exploration tasks

---

## Invocation Example

When user says: "Implement a password validator"

Response:
```
Starting Design-First TDD workflow for: Password Validator

📐 DESIGN Phase
───────────────
Invoking spec-designer agent to analyze requirements and define contracts...

[spec-designer output]

Does this design capture your requirements? May I proceed to writing tests?
```

After user approval:
```
🔴 RED Phase
────────────
Invoking tdd-test-writer agent to create failing tests...

[tdd-test-writer output]

All 12 tests are failing as expected. Ready to implement?
```

After user approval:
```
🟢 GREEN Phase
──────────────
Invoking tdd-implementer agent to write minimal implementation...

[tdd-implementer output]

All 12 tests are now passing. Running coverage analysis...
```

Automatically proceeds:
```
📊 COVERAGE Phase
─────────────────
Invoking coverage-analyst agent to analyze test coverage...

[coverage-analyst output]

Coverage targets met (94% line, 88% branch). Proceeding to refactor phase.
```

Automatically proceeds:
```
🔵 REFACTOR Phase
─────────────────
Invoking tdd-refactorer agent to improve code quality...

[tdd-refactorer output]

TDD cycle complete. Password Validator feature implementation finished.
```

---

## Remember

The goal of this workflow is to prevent:
1. Implementation-first development
2. Tests written after implementation
3. Incomplete test coverage
4. Over-engineering during implementation

By isolating each phase in a separate agent, we ensure that:
1. Contracts are defined before tests
2. Tests are written before implementation
3. Implementation is minimal
4. Coverage gaps trigger iteration
5. Refactoring preserves behavior

**Trust the process. Follow the phases. Ship quality code.**
