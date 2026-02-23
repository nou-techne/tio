# TIO RACI Matrix: Cross-Team Sprint Coordination (v2.0)

Enhances dependency-driven planning by clarifying roles across sprints. **RACI**: *Responsible* (does work), *Accountable* (owns outcome), *Consulted* (input required), *Informed* (kept updated).

## Roles (15 Complete)
| Abbr | Role |
|------|------|
| PE | 00 Product Engineer |
| TL | 00 Technical Lead |
| SA | 01 Schema Architect |
| BE | 02 Backend Engineer |
| IE | 03 Integration Engineer |
| ESE | 04 Event Systems Engineer |
| WE | 05 Workflow Engineer |
| CS | 06 Compliance/Security |
| FD | 07 Frontend/DevOps |
| TCW | 08 Technical Community Writer |
| QATE | QA/Test Engineer |
| BDS | 09 Bio-Regional Data Steward |
| VA | 10 Venture Architect |
| ESEng | 11 Economic Systems Engineer |
| TA | 12 Tax Accountant |
| MA | 13 Management Accountant |
| CL | 14 Co-op Lawyer |
| SL | 15 Securities Lawyer |

## Key Processes

| Process | PE | TL | SA | BE | IE | ESE | WE | CS | FD | TCW | QATE | BDS | VA | ESEng | TA | MA | CL | SL |
|---------|----|----|----|----|----|----|----|----|----|-----|------|-----|----|-------|----|----|----|----|
| **Sprint Planning**<br>Define backlog, deps | R | A | C | C | C | C | C | I | I | I | I | C | C | C | I | I | I | I |
| **Sprint Kickoff**<br>Assign tasks, align | C | A | R | R | R | R | R | C | C | I | I | I | I | I | I | I | I | I |
| **Daily Standup**<br>Blockers, progress | I | I | C | R | R | R | R | I | I | I | C | I | I | I | I | I | I | I |
| **Cross-Sprint Handoff**<br>Artifact review, deps | C | A | R | C | C | C | C | C | I | R | C | C | R | C | I | C | I | I |
| **PR Review**<br>Code quality gate | I | C | C | R | R | R | R | C | C | I | R | I | I | I | I | I | I | I |
| **Deployment**<br>Release to staging/prod | I | A | I | C | C | C | C | R | R | I | R | I | I | I | I | I | I | I |
| **Retro/Feedback**<br>Lessons, improve | R | A | C | C | C | C | C | I | I | R | C | C | C | C | I | C | I | I |
| **Docs Update**<br>Post-sprint artifacts | I | I | C | I | I | I | I | I | I | R | I | C | C | I | I | I | I | I |
| **Economic/Financial Review**<br>Allocations, models | I | C | I | I | I | I | I | C | I | I | I | I | C | R | R | R | C | C |
| **Legal/Compliance Gate**<br>Bylaws, tokens | I | C | I | I | I | I | I | R | I | I | I | I | I | C | C | I | R | R |

## Guidelines
- **Consulted (C)**: Mandatory sync (Slack/15min).
- **Informed (I)**: Async (#tio-sprint).
- **Escalation**: TL owns; >24h stall → #tio-escalate.
- **Assignments**: Per-sprint in SPRINT_BLOCKERS.md + roles/.

*Quarterly review; v2.0 adds 09-15 roles + econ/legal processes.*

