# Testing Strategy

LineProof testing should prove that fairness, escrow, and identity invariants hold across contract, SDK, and application boundaries.

## Test Layers

| Layer                      | Location                  | Purpose                                                          |
| -------------------------- | ------------------------- | ---------------------------------------------------------------- |
| Contract unit tests        | `contracts/*/src/test.rs` | Validate individual Soroban contract behavior.                   |
| Contract integration tests | `contracts/` workspace    | Validate queue, enrollment, identity, and escrow interactions.   |
| SDK tests                  | `sdk/tests/`              | Validate typed client behavior and public API expectations.      |
| Reference app tests        | `frontend/`, `backend/`   | Validate UI/API behavior without redefining protocol guarantees. |
| End-to-end examples        | `examples/`               | Validate realistic domain flows against localnet.                |

## Required Invariants

- A queue cannot exceed capacity.
- Enrollment cannot occur outside `EnrollmentOpen`.
- A participant cannot enroll twice in the same queue.
- A position cannot advance twice.
- Closed queues are terminal.
- Non-transferable positions cannot move to another identity.
- Escrow cannot be released and refunded.
- Expiry cannot happen before the configured hold period.
- Events contain enough data to reconstruct the queue history.

## Commands

```bash
make test-contracts
make test-sdk
make test
make lint
```

## Property-Based Testing

Property-based tests are planned for lifecycle and escrow state machines. Useful generators include:

- Random legal and illegal lifecycle transition sequences.
- Enrollment attempts under capacity and duplicate constraints.
- Escrow state transitions with random timing.
- Batch advancement sizes across queue lengths.

## Localnet Integration

Localnet tests should verify real transaction construction, event decoding, contract IDs, and asset transfer behavior. SDK tests that only mock responses are useful but not sufficient for release readiness.

## CI Expectations

Pull requests should run formatting, linting, contract tests, SDK tests, and security scans. Documentation-only changes may skip expensive integration jobs if CI supports path filters, but changes to contracts, SDK types, deployment scripts, or examples should run the full suite.

### Coverage Enforcement

- **Backend**: Minimum 65% lines, 65% functions, 55% branches, 65% statements
- **SDK**: Minimum 60% lines, 60% functions, 50% branches, 60% statements
- Coverage is measured and enforced on every CI run via `pnpm test:coverage`
- PRs that reduce coverage below thresholds will fail CI
- Coverage reports are generated as LCOV and HTML artifacts for inspection

## Release Criteria

Before tagging a release:

- All tests pass in CI.
- Changelog includes user-visible changes.
- Deployment notes include contract IDs or explicitly state that no deployment occurred.
- Security-sensitive changes have at least one reviewer focused on authorization and invariants.
- Docs match the current public API and contract behavior.
