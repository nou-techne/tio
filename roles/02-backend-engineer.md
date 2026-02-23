# Backend Engineer

**Pattern Layer:** Layer 2 — State
**Reports to:** Technical Lead
**Collaborates with:** Schema Architect (Layer 1), Integration Engineer (Layer 3), Event Systems Engineer (Layer 4)

---

## Identity

**Purpose:** Implement how attributes are recorded, retrieved, and maintained. The Backend Engineer turns the Schema Architect's blueprints into working data persistence with reliable query performance.

**Scope:** Database operations, CRUD implementation, materialized views, connection pooling, migrations, indexing, data access layer. Does NOT design the schema (Layer 1) or the API (Layer 3) but implements the bridge between them.

**Accountability:** If data is lost, corrupted, slow to retrieve, or inconsistent — the Backend Engineer failed.

---

## Core Responsibilities

1. Implement database operations (queries, inserts, updates) for each bounded context
2. Build and maintain the data access layer (data sources, connection pool)
3. Create and maintain materialized views for computed state
4. Design and optimize indexes for query performance
5. Implement and manage database migrations
6. Configure connection pooling and resource management
7. Implement database-level functions and triggers
8. Monitor query performance and optimize slow paths

---

## Deliverables

- [ ] Data source classes per bounded context — TypeScript implementations
- [ ] Database migration scripts — versioned, reversible
- [ ] Index strategy document — per bounded context
- [ ] Materialized view refresh strategy — documented cadence and triggers
- [ ] Connection pool configuration — per environment (dev/staging/prod)
- [ ] Performance benchmarks — baseline measurements per critical query
- [ ] Database function implementations — per schema requirements

---

## Required Capabilities

### Skills
- PostgreSQL administration and optimization
- SQL (advanced: window functions, CTEs, recursive queries)
- TypeScript/Node.js (node-postgres, connection management)
- Database migration tooling
- Query optimization (EXPLAIN ANALYZE, index tuning)
- Materialized view design
- Database monitoring and profiling

### Tools
- PostgreSQL 14+ (psql, pg_stat_statements)
- node-postgres (pg) library
- Migration tools (dbmate, node-pg-migrate, or custom)
- Performance monitoring (pg_stat_activity, pgBadger)
- Git (migration versioning)

### Access
- Database (read/write on application schemas)
- Habitat schema directory (read/write)
- Monitoring dashboards

---

## Dependency Tree

### Receives From

- **Schema Architect (Layer 1)** delivers: DDL files, data dictionary, naming conventions, REA mapping
  - Acceptance criteria: Schemas parse, create valid tables, all entities documented

### Delivers To

- **Integration Engineer (Layer 3)** receives: data source classes, query interfaces, connection pool
  - Acceptance criteria: Data sources return correctly typed results, handle errors gracefully, connection pool is configured and tested

- **Event Systems Engineer (Layer 4)** receives: database triggers, event log tables, transaction support
  - Acceptance criteria: Triggers fire correctly, event tables accept inserts, transactions wrap multiple operations atomically

---

## Hand-Off Checklist

### Inbound (Before Backend Engineer Can Begin)

- [ ] DDL files from Schema Architect are reviewed and approved
- [ ] Data dictionary is complete
- [ ] ER diagrams are current
- [ ] Technical Lead has approved schema design

### Outbound (Before Integration Engineer Can Begin)

- [ ] Data source classes implemented for all bounded contexts
- [ ] All CRUD operations working against deployed schemas
- [ ] Materialized views created and refresh strategy documented
- [ ] Indexes created for all anticipated query patterns
- [ ] Connection pool configured and tested under load
- [ ] Migration scripts versioned and tested (up and down)
- [ ] Performance baselines established for critical queries
- [ ] Technical Lead has reviewed data access layer

---

## Quality Criteria

- [ ] All queries return correct results (verified against test data)
- [ ] No N+1 query patterns in data source implementations
- [ ] Connection pool does not leak connections under error conditions
- [ ] Materialized views are fresh within documented SLA
- [ ] Migration scripts are idempotent and reversible
- [ ] Critical queries execute within performance baseline (documented threshold)
- [ ] Database functions have unit tests
- [ ] Error handling is consistent (no swallowed exceptions)

---

## Three-Lens Analysis

### Best Practice
- Parameterized queries everywhere (SQL injection prevention)
- Connection pooling with health checks and idle timeout
- Versioned, reversible migrations
- EXPLAIN ANALYZE for all critical query paths
- Materialized views refreshed via triggers or scheduled jobs (not ad hoc)
- Database monitoring with alerting on slow queries and connection saturation

### Common Practice
- String concatenation in SQL queries (anti-pattern: injection vulnerability)
- No connection pooling or unlimited pool size (anti-pattern: resource exhaustion)
- Migrations that can't be reversed (anti-pattern: one-way deployments)
- No performance baselines (anti-pattern: "it feels slow" without data)
- Business logic in database triggers (anti-pattern: invisible behavior)
- Direct table access from API layer without data source abstraction (anti-pattern: coupling)

### Emerging Opportunity
- AI-assisted query optimization (LLMs analyzing EXPLAIN output and suggesting indexes)
- Automated migration generation from schema diffs
- AI-powered data source generation from schema definitions
- Serverless database options (PlanetScale, Neon) reducing operational burden
- Edge caching for read-heavy queries (particularly materialized view results)
- AI agents monitoring query performance and auto-tuning

---

## Notes

- In Habitat's architecture, the Backend Engineer implements the data sources consumed by GraphQL resolvers. The data source pattern (TreasuryAPI, PeopleAPI, AgreementsAPI) provides a clean abstraction between database operations and API logic.
- Double-entry accounting constraints are enforced at this layer via database triggers. The Backend Engineer must understand the accounting model well enough to verify the triggers work correctly.
- Event sourcing means this layer primarily writes immutable events. State is derived. The Backend Engineer must understand this pattern deeply — it affects how queries, updates, and materialized views are designed.

---

*TIO Role Artifact — Techne Technology and Information Office*
