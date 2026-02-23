# QA and Test Engineer

**Pattern Layer:** Cross-Cutting (spans all seven layers)
**Reports to:** Technical Lead
**Collaborates with:** All pattern stack roles, Product Engineer

---

## Identity

**Purpose:** Validate that each layer's contract with the layers below is honored and that the whole system composes into correct, reliable behavior. The QA Engineer is the guardian of system integrity across the entire pattern stack.

**Scope:** Unit testing per layer, integration testing across layers, compliance verification, performance testing, regression testing, test automation, quality metrics. Does NOT implement features but verifies they work correctly and compose reliably.

**Accountability:** If a bug reaches production that was testable, or if a regression is introduced that was previously caught — the QA Engineer failed.

---

## Core Responsibilities

1. Define test strategy per layer and across layers
2. Write and maintain unit tests for each layer's deliverables
3. Write and maintain integration tests for cross-layer workflows
4. Build compliance verification suite (704(b) correctness)
5. Implement performance and load testing
6. Maintain regression test suite
7. Automate test execution in CI/CD pipeline
8. Track and report quality metrics (coverage, defect rates, regression frequency)
9. Validate hand-offs between layers (acceptance testing)
10. Conduct exploratory testing for edge cases

---

## Deliverables

- [ ] Test strategy document — per project/engagement
- [ ] Unit test suite — per layer, per bounded context
- [ ] Integration test suite — cross-layer workflow verification
- [ ] Compliance verification suite — 704(b), double-entry, allocation correctness
- [ ] Performance test suite — load testing, stress testing, benchmark results
- [ ] Regression test suite — automated, runs on every commit
- [ ] Test coverage report — per layer, with gap analysis
- [ ] Quality metrics dashboard — defect rates, coverage trends, test pass rates
- [ ] Hand-off acceptance tests — verify deliverables between layers
- [ ] Exploratory test reports — edge cases discovered, documented

---

## Required Capabilities

### Skills
- Test strategy design (pyramid: unit → integration → E2E)
- Unit testing (Jest, Vitest)
- Integration testing (database, API, event bus)
- Performance testing (k6, Artillery, or equivalent)
- Compliance testing (accounting verification, rule validation)
- Test automation and CI/CD integration
- Exploratory testing methodology
- SQL (for direct database verification)
- TypeScript (for test implementation)

### Tools
- Jest or Vitest (unit and integration tests)
- Supertest (API testing)
- k6 or Artillery (load testing)
- GitHub Actions (CI integration)
- PostgreSQL (direct query verification)
- Coverage reporters (Istanbul/c8)

### Access
- All bounded contexts (read, for verification)
- Test environments (full access)
- CI/CD pipeline (configuration)
- Monitoring dashboards (for performance baseline comparison)

---

## Dependency Tree

### Receives From

- **All Pattern Stack Roles** deliver: completed layer work with documented acceptance criteria
  - Acceptance criteria: Work is complete enough to test, acceptance criteria are clear

- **Technical Lead** delivers: test strategy direction, quality standards, priority guidance
  - Acceptance criteria: Clear definition of what "done" means at each layer

- **Product Engineer** delivers: acceptance criteria from user perspective
  - Acceptance criteria: User-facing quality criteria are testable

### Delivers To

- **Technical Lead** receives: quality reports, test results, regression findings, coverage analysis
  - Acceptance criteria: Actionable reports with severity classification

- **All Pattern Stack Roles** receive: bug reports, regression alerts, test failures
  - Acceptance criteria: Defects are reproducible, classified by layer and severity

- **Product Engineer** receives: quality confidence assessment (is this ready to ship?)
  - Acceptance criteria: Honest assessment of quality with known risk areas identified

---

## Hand-Off Checklist

### Inbound (Before QA Can Test a Layer)

- [ ] Layer deliverables are complete (from owning role)
- [ ] Acceptance criteria are documented
- [ ] Test environment is available and configured
- [ ] Test data exists or can be generated

### Outbound (Before Release)

- [ ] All unit tests pass
- [ ] All integration tests pass
- [ ] Compliance verification suite passes
- [ ] Performance benchmarks meet SLA
- [ ] No critical or high severity bugs unresolved
- [ ] Regression suite passes (no new regressions)
- [ ] Coverage meets threshold (target: 80%+ for critical paths)
- [ ] Quality metrics reviewed with Technical Lead

---

## Quality Criteria

- [ ] Test pyramid is healthy (many unit, moderate integration, few E2E)
- [ ] Critical paths have 90%+ coverage
- [ ] All compliance tests pass (zero tolerance for 704(b) violations)
- [ ] Regression suite runs on every commit and PR
- [ ] Performance benchmarks are tracked over time (no silent degradation)
- [ ] Defects are found before users find them
- [ ] Test suite runs in under 10 minutes (fast feedback)
- [ ] Flaky tests are tracked and eliminated

---

## Layer-Specific Testing

### Layer 1 (Identity) — Schema Tests
- DDL parses and creates valid schemas
- Constraints enforce entity rules
- Identifier generation produces unique values

### Layer 2 (State) — Data Access Tests
- CRUD operations return correct results
- Materialized views reflect current state
- Connection pool handles concurrent requests

### Layer 3 (Relationship) — API Tests
- GraphQL queries return expected data
- Cross-context traversals resolve correctly
- Pagination works correctly
- Authorization restricts data per role

### Layer 4 (Event) — Event Tests
- Events publish correctly from mutations
- Handlers receive and process events
- Idempotency prevents duplicate processing
- Dead-letter queue captures failures

### Layer 5 (Flow) — Workflow Tests
- End-to-end flows complete successfully
- Partial failure triggers compensation
- Period close produces correct allocations

### Layer 6 (Constraint) — Compliance Tests
- 704(b) capital accounts maintained correctly
- Double-entry transactions balance to zero
- RLS policies restrict data correctly
- Authentication cannot be bypassed

### Layer 7 (View) — UI and Deployment Tests
- UI renders correctly across devices
- Accessibility passes automated checks
- Deployments succeed without downtime
- Monitoring alerts fire on test anomalies

---

## Three-Lens Analysis

### Best Practice
- Test pyramid (unit > integration > E2E)
- Tests run on every commit (CI gate)
- Compliance tests as first-class citizens (not afterthought)
- Performance baselines tracked over time
- Exploratory testing alongside automated testing
- Test data management (factories, fixtures, teardown)
- Coverage as a trend metric, not a gate (80% is a guide, not a rule)

### Common Practice
- Testing only at the E2E level (anti-pattern: slow, brittle, expensive)
- No compliance testing (anti-pattern: discovered at audit time)
- Performance testing only before launch (anti-pattern: silent degradation)
- Manual regression testing (anti-pattern: slow, incomplete, boring)
- Flaky tests tolerated (anti-pattern: erodes confidence in test suite)
- Test environment differs from production (anti-pattern: "works in test")

### Emerging Opportunity
- AI-assisted test generation (LLMs generating test cases from specifications)
- Property-based testing (generating random valid inputs to find edge cases)
- AI-powered test prioritization (run tests most likely to fail first)
- Mutation testing (verify tests actually catch bugs)
- AI agents that conduct exploratory testing autonomously
- Visual regression testing for UI (screenshot comparison)
- Compliance testing as continuous monitoring (not just at release)

---

## Notes

- The QA Engineer's cross-cutting nature means they are the first to understand how the whole system composes. This perspective is invaluable — the QA Engineer often discovers integration issues that no single-layer engineer can see.
- Compliance testing in Habitat is not optional. The system must produce IRS-auditable results. The QA Engineer's compliance verification suite is the proof that it does.
- In Techne's cooperative context, quality is a form of care. Members trust the system with their economic lives. Bugs in patronage calculation aren't just software defects — they're breaches of trust.

---

*TIO Role Artifact — Techne Technology and Information Office*
