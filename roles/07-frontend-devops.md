# Frontend and DevOps Engineer

**Pattern Layer:** Layer 7 — View
**Reports to:** Technical Lead
**Collaborates with:** Integration Engineer (Layer 3), Compliance and Security Engineer (Layer 6), Product Engineer

---

## Identity

**Purpose:** Present information for a purpose — both to humans (UI) and to operations (monitoring, deployment, observability). The Frontend and DevOps Engineer makes everything below visible, usable, and maintainable.

**Scope:** Member dashboard, contribution forms, allocation review interfaces, deployment pipelines, CI/CD, monitoring dashboards, container orchestration, infrastructure management. Owns both the human-facing view and the operations-facing view.

**Accountability:** If members can't interact with the system, or if the operations team can't see what's happening in production — the Frontend and DevOps Engineer failed.

---

## Core Responsibilities

1. Build member-facing UI (dashboard, forms, review interfaces)
2. Implement responsive, accessible design (WCAG 2.1 AA)
3. Integrate with GraphQL API using typed client SDK
4. Implement authentication flows (login, logout, session management)
5. Build and maintain CI/CD pipelines (GitHub Actions)
6. Manage container orchestration (Docker Compose, Kubernetes)
7. Configure monitoring and alerting (Prometheus, Grafana, Loki)
8. Manage deployment environments (dev, staging, production)
9. Implement performance optimization (caching, code splitting, CDN)
10. Build operational dashboards (system health, business metrics)

---

## Deliverables

- [ ] Member dashboard — patronage summary, contribution history, capital account view
- [ ] Contribution submission forms — per type (labor, expertise, capital, relationship)
- [ ] Approver interface — pending queue, approve/reject workflow
- [ ] Allocation review interface — period close status, proposed allocations
- [ ] CI/CD pipeline — build, test, deploy automation
- [ ] Docker images — per service (API, Worker, Frontend)
- [ ] Monitoring dashboards — system overview, API performance, business metrics
- [ ] Deployment runbook — per environment
- [ ] Performance benchmarks — page load times, API response times
- [ ] Accessibility audit results — WCAG compliance

---

## Required Capabilities

### Skills
- React / Next.js (TypeScript)
- GraphQL client (urql, Apollo Client)
- CSS/Tailwind, responsive design
- Accessibility (WCAG 2.1 AA)
- Lucide iconography (Techne design system)
- Docker and container orchestration
- CI/CD (GitHub Actions)
- Monitoring (Prometheus, Grafana, Loki)
- Infrastructure management (DNS, TLS, load balancing)
- Performance optimization

### Tools
- Next.js 14+ (App Router)
- Tailwind CSS + shadcn/ui + Radix
- GraphQL Code Generator (typed hooks)
- Docker and Docker Compose
- GitHub Actions
- Prometheus + Grafana + Loki
- Caddy (reverse proxy, TLS)

### Access
- Frontend repository (read/write)
- CI/CD pipeline configuration
- Container registry
- Infrastructure (servers, DNS, TLS certificates)
- Monitoring dashboards (admin)

---

## Dependency Tree

### Receives From

- **Integration Engineer (Layer 3)** delivers: typed client SDK, GraphQL schema, API documentation
  - Acceptance criteria: SDK generates correctly typed hooks, all needed queries/mutations are available

- **Compliance and Security Engineer (Layer 6)** delivers: authentication flow, authorization rules, security headers
  - Acceptance criteria: Login/logout works, role-based UI visibility is defined, security headers are specified

- **Workflow Engineer (Layer 5)** delivers: workflow state for UI display
  - Acceptance criteria: Workflow progress is queryable for status indicators

- **Product Engineer** delivers: UI requirements, wireframes, user flow specifications
  - Acceptance criteria: Clear specification of what each screen shows and does

### Delivers To

- **Members / Users** receive: functional, accessible, performant UI
  - Acceptance criteria: Members can complete all core tasks (submit contributions, view allocations, etc.)

- **Operations Team** receives: monitoring dashboards, deployment automation, alerting
  - Acceptance criteria: System health is visible, deployments are automated, alerts fire on anomalies

- **Product Engineer** receives: analytics data, user feedback integration
  - Acceptance criteria: Usage patterns are trackable, feedback can be collected

---

## Hand-Off Checklist

### Inbound (Before Frontend/DevOps Can Begin)

- [ ] Client SDK generated from GraphQL schema (from Integration Engineer)
- [ ] Authentication flow implemented (from Compliance Engineer)
- [ ] UI requirements and wireframes exist (from Product Engineer)
- [ ] Design system defined (colors, typography, Lucide icons)
- [ ] Technical Lead has approved frontend architecture

### Outbound (Before Launch)

- [ ] All UI screens functional and tested
- [ ] Accessibility audit passes (WCAG 2.1 AA)
- [ ] CI/CD pipeline deploys all services automatically
- [ ] Monitoring dashboards are live and alerting is configured
- [ ] Performance benchmarks meet SLA (page load < 3s, API response < 500ms)
- [ ] Deployment runbook is documented and tested
- [ ] Docker images build and run correctly across all environments
- [ ] Technical Lead has reviewed frontend and infrastructure

---

## Quality Criteria

- [ ] All core user flows work end-to-end (submit contribution, view allocation, review as approver)
- [ ] UI is responsive (mobile, tablet, desktop)
- [ ] Accessibility: keyboard navigable, screen reader compatible, sufficient contrast
- [ ] No Lucide icon violations (no emoji in interfaces)
- [ ] Three-color system consistent (green primary, blue infrastructure, burnt orange action)
- [ ] CI/CD: push to main triggers test → build → deploy
- [ ] Monitoring: dashboards show all services, alerts fire within 5 minutes of anomaly
- [ ] Zero-downtime deployments (rolling updates)
- [ ] Performance: Lighthouse score > 90

---

## Three-Lens Analysis

### Best Practice
- Component library with design system (shadcn/ui, Storybook)
- Type-safe GraphQL integration (codegen hooks)
- Progressive enhancement (works without JavaScript for basic views)
- Automated accessibility testing in CI
- Blue-green or rolling deployments for zero downtime
- Infrastructure as code (Docker Compose, Terraform)
- Monitoring with the three pillars (metrics, logs, traces)

### Common Practice
- No design system (anti-pattern: inconsistent UI across screens)
- Manual GraphQL query strings (anti-pattern: type drift, runtime errors)
- Accessibility ignored (anti-pattern: excludes users, legal liability)
- Manual deployments (anti-pattern: human error, inconsistency)
- Monitoring added after launch (anti-pattern: blind to problems until users report)
- "Works on my machine" deployment (anti-pattern: environment drift)

### Emerging Opportunity
- AI-assisted UI generation from wireframes or descriptions
- Automated visual regression testing (Chromatic, Percy)
- Edge rendering (Next.js on edge functions for global performance)
- AI-powered monitoring (anomaly detection, predictive alerting)
- Design-to-code tools (Figma → React components)
- AI agents that test UI flows by simulating user behavior
- Real-time collaborative features (multiple members viewing allocation review simultaneously)

---

## Notes

- The Frontend and DevOps Engineer role is dual-natured: human-facing (UI) and operations-facing (deployment/monitoring). In a larger team, these split into separate roles. For MVP, one person holds both.
- Techne's design system: Lucide iconography (no emoji), three-color system (green, blue, burnt orange), clean typography. The Frontend Engineer enforces this consistently.
- DevOps in Habitat's context means the system is self-hosting capable. A cooperative should be able to deploy and maintain their own Habitat instance. The deployment documentation and automation must be clean enough for a non-expert to follow.
- Monitoring serves two audiences: the operations team (is the system healthy?) and the cooperative (are patronage activities being tracked correctly?). Business metrics dashboards are as important as system metrics dashboards.

---

*TIO Role Artifact — Techne Technology and Information Office*
