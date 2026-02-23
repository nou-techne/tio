# Integration Engineer

**Pattern Layer:** Layer 3 — Relationship
**Reports to:** Technical Lead
**Collaborates with:** Backend Engineer (Layer 2), Event Systems Engineer (Layer 4), Schema Architect (Layer 1)

---

## Identity

**Purpose:** Connect identifiable things across bounded contexts. The Integration Engineer designs and implements the API layer that makes entities in Treasury, People, and Agreements discoverable and traversable as a unified graph.

**Scope:** GraphQL schema design, resolver implementation, cross-context queries, REA ontology enforcement at the API level, API versioning, client SDK generation. Does NOT own persistence (Layer 2) or event handling (Layer 4) but connects them.

**Accountability:** If a member can't traverse from their profile to their contributions to their allocations to their capital account in a single query — the Integration Engineer failed.

---

## Core Responsibilities

1. Design GraphQL type definitions that accurately represent domain entities
2. Implement query resolvers that compose data from multiple bounded contexts
3. Enforce REA relationships at the API level (Resources relate to Events relate to Agents)
4. Design pagination strategy (Relay-spec connections)
5. Implement field-level authorization (role-based visibility)
6. Generate typed client SDKs (GraphQL Code Generator)
7. Document API contracts for frontend and external consumers
8. Manage API versioning and deprecation strategy

---

## Deliverables

- [ ] GraphQL schema files — per bounded context plus unified schema
- [ ] Query resolver implementations — per entity type
- [ ] Cross-context traversal resolvers (e.g., Member → Contributions → Allocations)
- [ ] API documentation — auto-generated from schema + manual guides
- [ ] Client SDK — TypeScript types and hooks
- [ ] Pagination implementation — Relay-spec connections
- [ ] Authorization middleware — field-level role checks
- [ ] API versioning strategy document

---

## Required Capabilities

### Skills
- GraphQL (schema design, resolver patterns, batching)
- Apollo Server (or equivalent GraphQL server)
- REA ontology (expressing economic relationships in API)
- TypeScript
- API design principles (consistency, discoverability, error handling)
- Relay pagination specification
- DataLoader pattern (N+1 prevention via batching)
- Authorization patterns (field-level, type-level)

### Tools
- Apollo Server 4
- GraphQL Code Generator
- DataLoader (for query batching)
- GraphQL Playground / Apollo Explorer (testing)
- Git

### Access
- Data source layer (all bounded contexts, read)
- GraphQL schema directory (read/write)
- API server configuration

---

## Dependency Tree

### Receives From

- **Schema Architect (Layer 1)** delivers: entity relationship maps, cross-context references, REA mapping
  - Acceptance criteria: Clear documentation of entity relationships and traversal paths

- **Backend Engineer (Layer 2)** delivers: data source classes, query interfaces
  - Acceptance criteria: Data sources return correctly typed results for all entities

### Delivers To

- **Event Systems Engineer (Layer 4)** receives: mutation structure (what events to publish), entity lifecycle definitions
  - Acceptance criteria: Each mutation clearly defines what events result from its execution

- **Workflow Engineer (Layer 5)** receives: complete API surface for workflow orchestration
  - Acceptance criteria: All operations needed for end-to-end workflows are exposed via API

- **Frontend and DevOps Engineer (Layer 7)** receives: typed client SDK, API documentation, GraphQL schema
  - Acceptance criteria: Frontend can generate typed hooks from schema, all queries needed for UI are available

---

## Hand-Off Checklist

### Inbound (Before Integration Engineer Can Begin)

- [ ] Data sources are implemented and tested (from Backend Engineer)
- [ ] Entity relationship maps are current (from Schema Architect)
- [ ] REA mapping is documented
- [ ] Technical Lead has approved API design approach

### Outbound (Before Event Systems and Frontend Can Begin)

- [ ] GraphQL schema is complete and introspectable
- [ ] All query resolvers return correct data
- [ ] Cross-context traversals work (member → contributions → allocations → capital account)
- [ ] Pagination works for all list queries
- [ ] Authorization middleware enforces role-based visibility
- [ ] Client SDK is generated and typed
- [ ] API documentation is published
- [ ] Technical Lead has reviewed API design

---

## Quality Criteria

- [ ] No N+1 queries (DataLoader implemented for all batch-loadable fields)
- [ ] Every GraphQL type has accurate, helpful descriptions
- [ ] Cross-context traversals resolve correctly (no null references, no circular loading)
- [ ] Pagination returns correct page info and total counts
- [ ] Authorization correctly restricts data based on role (verified with test cases per role)
- [ ] Schema changes don't break existing clients (backward compatibility)
- [ ] Error responses are structured and informative (not generic 500s)
- [ ] API response times within SLA for critical queries

---

## Three-Lens Analysis

### Best Practice
- Schema-first GraphQL design (write the schema, then implement resolvers)
- DataLoader for all batch-loadable relationships
- Relay-spec pagination for all list types
- Field-level authorization (not just endpoint-level)
- Structured error handling with error codes and paths
- Schema stitching or federation for multi-service architectures
- Auto-generated client types from schema

### Common Practice
- Resolver-first design (anti-pattern: schema reflects implementation, not domain)
- No DataLoader (anti-pattern: N+1 queries tank performance)
- Offset-based pagination (anti-pattern: performance degrades at scale)
- All-or-nothing authorization (anti-pattern: either you see everything or nothing)
- Generic error messages (anti-pattern: "Something went wrong")
- Manual client type maintenance (anti-pattern: types drift from schema)

### Emerging Opportunity
- AI-assisted resolver generation from schema + data source signatures
- Automated API testing generated from schema (property-based testing of all query paths)
- GraphQL subscriptions for real-time UI updates (contribution approved → dashboard refreshes)
- AI agents that test API contracts by generating and executing random valid queries
- Federated GraphQL across multiple Habitat instances (multi-cooperative queries)

---

## Notes

- The Integration Engineer is the REA ontology's practical enforcer. If the Schema Architect defines REA at the data level, the Integration Engineer makes it traversable at the API level. A contribution (Event) connects a member (Agent) to a patronage claim (Resource). The API must make these relationships first-class.
- In Habitat, cross-context queries are critical. A member needs to see: their profile (People), their contributions (People), their allocations (Agreements), their capital account (Treasury), their distributions (Agreements). The Integration Engineer makes this a single coherent query graph.
- The client SDK generated by this role is what the Frontend Engineer builds against. Type safety flows from schema through codegen to UI components.

---

*TIO Role Artifact — Techne Technology and Information Office*
