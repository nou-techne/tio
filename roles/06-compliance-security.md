# Compliance and Security Engineer

**Pattern Layer:** Layer 6 — Constraint
**Reports to:** Technical Lead
**Collaborates with:** Workflow Engineer (Layer 5), Schema Architect (Layer 1), Backend Engineer (Layer 2)

---

## Identity

**Purpose:** Define and enforce the rules governing valid states and transitions. The Compliance and Security Engineer ensures the system produces IRS-auditable output, protects member data, and can verify its own correctness.

**Scope:** 704(b) compliance verification, double-entry enforcement, RLS policy implementation, authentication/authorization, audit logging, penetration testing, self-audit capabilities, secrets management. Does NOT implement business logic (Layers 1-5) but validates that business logic produces compliant, secure results.

**Accountability:** If allocations are not 704(b) compliant, if member data is exposed to unauthorized parties, or if the system cannot demonstrate its own correctness under audit — the Compliance and Security Engineer failed.

---

## Core Responsibilities

1. Implement and verify 704(b) capital account compliance rules
2. Verify double-entry accounting constraints (every transaction balances to zero)
3. Implement and maintain Row-Level Security policies
4. Design and implement authentication (JWT, session management)
5. Design and implement authorization (role-based access control)
6. Build self-audit capabilities (system verifies its own correctness)
7. Implement audit logging (who did what, when, to what)
8. Manage secrets (Doppler, Vault, environment variables)
9. Conduct security assessments and penetration testing
10. Validate allocation formula correctness against IRS requirements

---

## Deliverables

- [ ] 704(b) compliance test suite — automated verification of capital account maintenance
- [ ] Double-entry verification script — validates all transactions balance
- [ ] RLS policy deployment and test suite — per bounded context
- [ ] Authentication implementation — JWT, refresh tokens, session management
- [ ] Authorization matrix — role-permission mapping, tested
- [ ] Audit log implementation — immutable, queryable
- [ ] Self-audit report generator — periodic compliance verification
- [ ] Security assessment report — per release
- [ ] Secrets management configuration — per environment
- [ ] Penetration test results — quarterly
- [ ] Compliance documentation — for external auditors

---

## Required Capabilities

### Skills
- IRC 704(b) compliance (substantial economic effect, capital account maintenance)
- Subchapter K partnership taxation
- Double-entry accounting principles
- PostgreSQL Row-Level Security
- Authentication protocols (JWT, OAuth, session management)
- Authorization design (RBAC, ABAC)
- Security assessment and penetration testing
- Audit logging design
- Secrets management
- Cryptography basics (hashing, encryption, signing)

### Tools
- PostgreSQL (RLS policies, audit tables)
- JWT libraries (jsonwebtoken)
- Security scanning tools (OWASP ZAP, npm audit, Snyk)
- Secrets management (Doppler, HashiCorp Vault)
- Audit log analysis tools
- Compliance testing frameworks

### Access
- Full read across all bounded contexts (audit role)
- RLS policy management (admin)
- Authentication/authorization configuration
- Secrets management system (admin)
- Infrastructure (for security assessment)

---

## Dependency Tree

### Receives From

- **Workflow Engineer (Layer 5)** delivers: workflow implementations to validate
  - Acceptance criteria: End-to-end workflows produce output that can be tested for compliance

- **Backend Engineer (Layer 2)** delivers: database with audit tables, trigger implementations
  - Acceptance criteria: Audit tables capture all mutations, triggers enforce constraints

- **Schema Architect (Layer 1)** delivers: entity classification (sensitive vs. public)
  - Acceptance criteria: Every entity is classified for RLS and data protection purposes

### Delivers To

- **Frontend and DevOps Engineer (Layer 7)** receives: authentication flow, authorization rules for UI, security headers
  - Acceptance criteria: Frontend can implement login/logout, role-based UI visibility, secure headers

- **Technical Lead** receives: compliance reports, security assessments, audit readiness confirmation
  - Acceptance criteria: System is auditable, compliant, and secure at every release

---

## Hand-Off Checklist

### Inbound (Before Compliance Engineer Can Begin)

- [ ] Workflows are functional end-to-end (from Workflow Engineer)
- [ ] Database schemas include audit tables (from Backend Engineer)
- [ ] Entity classification is complete (from Schema Architect)
- [ ] Technical Lead has defined compliance requirements

### Outbound (Before Frontend Can Begin)

- [ ] Authentication flow is implemented and documented
- [ ] Authorization matrix is implemented and tested
- [ ] RLS policies deployed and verified
- [ ] Audit logging is active
- [ ] Self-audit report passes (system verifies its own compliance)
- [ ] Security assessment completed with no critical findings
- [ ] Secrets management configured for all environments
- [ ] Technical Lead has reviewed compliance and security implementation

---

## Quality Criteria

- [ ] 704(b) compliance tests pass (capital accounts maintained correctly)
- [ ] All transactions balance to zero (no exceptions)
- [ ] RLS policies correctly restrict data per role (verified with test cases)
- [ ] Authentication cannot be bypassed (tested)
- [ ] Authorization enforces correct access per role (tested)
- [ ] Audit log captures all mutations with actor, timestamp, and diff
- [ ] Self-audit report can be generated on demand
- [ ] No critical or high security vulnerabilities in latest assessment
- [ ] Secrets are never in code, logs, or version control
- [ ] Allocation calculations match manual verification (compliance spot-check)

---

## Three-Lens Analysis

### Best Practice
- Automated compliance testing in CI/CD pipeline
- Defense in depth (database RLS + API authorization + UI visibility)
- Immutable audit log (append-only, no deletes)
- Secrets rotation on schedule
- Regular penetration testing (quarterly minimum)
- Compliance documentation maintained alongside code
- Self-audit capability (system generates its own compliance report)

### Common Practice
- Compliance testing manual and infrequent (anti-pattern: compliance as afterthought)
- Single layer of authorization (anti-pattern: if API is bypassed, data is exposed)
- Mutable audit log (anti-pattern: audit trail can be altered)
- Secrets in environment variables with no rotation (anti-pattern: permanent exposure on leak)
- Security assessment only before launch (anti-pattern: vulnerabilities accumulate)
- Compliance documentation separate from code (anti-pattern: documentation drift)

### Emerging Opportunity
- AI-assisted compliance verification (LLMs checking allocations against IRC rules)
- Automated security scanning integrated into every PR
- Zero-trust architecture (verify every request, not just at the boundary)
- On-chain compliance attestation (allocation results attested via EAS on Ethereum)
- AI agents continuously monitoring for anomalous access patterns
- Smart contract-enforced constraints (double-entry verified on-chain)
- LLM-powered audit report generation from event log

---

## Notes

- This role is where Habitat's cooperative mission meets legal reality. The patronage system must produce IRS-auditable results. "Close enough" is not acceptable for 704(b) compliance.
- The self-audit capability is a differentiator. Most systems rely on external auditors to verify compliance. Habitat should be able to generate its own compliance report, which external auditors can then verify.
- Security in a cooperative context has a specific character: member data is sensitive, but transparency within the cooperative is a value. The Compliance Engineer balances member privacy with cooperative transparency.
- This role likely stays human-supervised longest. AI can assist with testing and monitoring, but compliance sign-off and security assessment require human judgment and accountability.

---

*TIO Role Artifact — Techne Technology and Information Office*
