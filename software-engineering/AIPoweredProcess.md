# AI-Powered Product Engineering Process Framework

> **Enabling Every Team to Build Like a Product Engineer**
>
> Version 1.0 · April 2026

---

## Table of Contents

1. [Overview & Purpose](#1-overview--purpose)
2. [Configuring Claude Code for Your Project](#2-configuring-claude-code-for-your-project)
3. [Phase 1 — Strategy & KPIs](#3-phase-1--strategy--kpis)
4. [Phase 2 — Roadmap Planning](#4-phase-2--roadmap-planning)
5. [Phase 3 — Requirements & User Story Creation](#5-phase-3--requirements--user-story-creation)
6. [Phase 4 — Design & UX](#6-phase-4--design--ux)
7. [Phase 5 — Implementation with AI Agent](#7-phase-5--implementation-with-ai-agent)
8. [Phase 6 — Code Review (Human)](#8-phase-6--code-review-human)
9. [Phase 7 — Testing & QA (Human)](#9-phase-7--testing--qa-human)
10. [Phase 8 — Release & Monitoring](#10-phase-8--release--monitoring)
11. [Regulatory Compliance Framework](#11-regulatory-compliance-framework)
12. [Roles & Responsibilities](#12-roles--responsibilities)
13. [End-to-End Process Flow](#13-end-to-end-process-flow)
14. [Quick Reference — Agent Commands](#14-quick-reference--agent-commands)

---

## 1. Overview & Purpose

This document defines the end-to-end process framework that enables every team at the organization to operate as a full-stack product engineering team — from idea to production — using AI agents as force multipliers at each stage.

> 🎯 **Goal:** Any team — regardless of size or technical depth — can independently take a business idea from concept to a production-ready feature, with AI agents automating the repetitive, error-prone steps while **humans retain strategic control and quality assurance** .

### 1.1 Who This Process Is For

This framework applies to all product teams including:

- Product squads (PM + Engineers + Designer)
- Platform and infrastructure teams
- Data and analytics teams
- Business operations teams building internal tooling

### 1.2 What This Process Covers

| Phase | Primary Owner | AI Agent Role |
|-------|---------------|---------------|
| 1. Strategy & KPIs | Product Manager | Research & benchmarking assistance |
| 2. Roadmap Planning | Product Manager | Prioritization modeling |
| 3. Requirements Writing | PM + Tech Lead | Auto-generate user stories in Jira |
| 4. Design & UX | Designer + PM | Component generation suggestions |
| 5. Implementation | Engineers + AI Agent | Branch creation, code generation, PR |
| 6. Code Review | Human Engineer | Review & approve |
| 7. Testing & QA | Human QA / Engineer | Human validation + monitoring checks |
| 8. Release & Monitoring | Engineers + DevOps | Auto-monitoring setup, alerting |

---

## 2. Configuring AI Coding Agents In Your Project

AI agents are the execution layer of this framework. While this process uses **Claude Code as the reference implementation**, teams can use multiple AI coding agents depending on their workflow, security needs, and maturity.

> 🎯 **Principle:** The process is **agent-agnostic** — tools can change, but the workflow, controls, and quality gates remain consistent.

### 2.1 Supported AI Coding Agents

The following agents are commonly used in modern engineering teams:

#### 🧠 Claude Code (Primary Reference Agent)

Claude Code is a CLI-based, repository-aware AI agent that understands your project context and automates development tasks.

**Strengths:**
- Deep understanding of large codebases (long context)
- Strong reasoning and structured outputs (plans, PRs, docs)
- Excellent at following constraints (`CLAUDE.md`)
- Ideal for multi-step workflows (plan → confirm → implement)

**Limitations:**
- Slower than inline tools like Copilot
- Requires structured prompts
- CLI workflow may require onboarding

**Best Use Cases:**
- Full feature implementation
- Refactoring across services
- Regulatory-aware development
- PR generation and documentation

**References / Case Studies:**
- https://www.anthropic.com/customers
- https://www.anthropic.com/news

---

#### 🤖 Codex / API-Based Agents

API-driven coding agents that can be embedded into internal tools and automation pipelines.

**Strengths:**
- Highly customizable and flexible
- Can integrate with Jira, CI/CD, Slack, Git workflows
- Enables building internal AI platforms

**Limitations:**
- Requires engineering effort to operationalize
- Less structured out-of-the-box
- Context management must be implemented

**Best Use Cases:**
- Internal developer platforms
- Automated workflows (Jira → Code → PR)
- Backend-heavy automation pipelines

**References / Case Studies:**
- https://platform.openai.com/examples
- https://openai.com/customers

---

#### 💡 GitHub Copilot

IDE-based assistant providing real-time code suggestions.

**Strengths:**
- Fast inline suggestions
- Minimal setup
- Great for boilerplate and repetitive tasks
- Improves individual developer speed

**Limitations:**
- Limited system-level understanding
- Not workflow-aware (no PR/story ownership)
- Weak enforcement of compliance rules

**Best Use Cases:**
- Writing functions and components
- Day-to-day development acceleration
- Supporting junior engineers

**References / Case Studies:**
- https://github.blog/category/copilot/
- https://github.blog/2023-06-27-the-impact-of-github-copilot-on-developer-productivity-and-happiness/

---

### 2.2 Recommended Hybrid Approach

Most high-performing teams use a **combination of agents**:

| Task | Recommended Agent |
|------|------------------|
| Story generation | Claude / Codex |
| Implementation planning | Claude |
| Code writing (inline) | Copilot |
| PR generation | Claude / Codex |
| Refactoring | Claude |
| Quick fixes | Copilot |

> ✅ **Key Insight:**  
> Use **Copilot for speed**, **Claude for correctness**, and **Codex for automation pipelines**.

---

### 2.3 Standardizing Agent Behavior

Regardless of which agent is used:

> ⚠️ **Rule:** Every agent must operate against a shared project context file (e.g., `CLAUDE.md` or equivalent).

This ensures:
- Consistent outputs
- Regulatory compliance enforcement
- Standardized PRs and stories
- Predictable behavior across teams

---

### 2.4 Installation & Prerequisites

For Claude Code (reference setup):

- Node.js 18+ installed
- Claude Code CLI: `npm install -g @anthropic-ai/claude-code`
- Git configured with repository access
- Jira API token with project write permissions
- GitHub / GitLab API token with repo and PR permissions

> 🔄 **Note:** Other agents (Codex, Copilot) will require their own setup, but must still follow this framework’s constraints.

### 2.2 The CLAUDE.md Project Configuration File

The single most important file for agent quality is `CLAUDE.md`, placed at the root of your repository. This file is read by Claude Code at the start of every session and tells the agent everything it needs to know about your project.

> ⚠️ **Rule:** Every repository that uses AI agents **MUST** have a `CLAUDE.md` file. Without it, the agent operates blindly. Treat this file with the same importance as your `README.md`.

#### 2.2.1 CLAUDE.md Required Sections

##### A. Project Identity

```markdown
## Project
Name: [Project Name]
Type: [Web App / API / Mobile / Data Pipeline / etc.]
Primary Language: [TypeScript / Python / Go / etc.]
Framework: [Next.js / FastAPI / Django / etc.]
Repository: [git URL]
Jira Project Key: [e.g., PLAT, CORE, DATA]
Team Slack Channel: #team-channel
```

##### B. Architecture Overview

```markdown
## Architecture
- Frontend: Next.js 14, deployed on Vercel
- Backend: FastAPI on AWS ECS
- Database: PostgreSQL (RDS), Redis for caching
- Message Queue: AWS SQS
- Monitoring: Datadog + PagerDuty
- CI/CD: GitHub Actions
```

##### C. Code Standards

```markdown
## Code Standards
- Language: TypeScript strict mode
- Formatter: Prettier (config: .prettierrc)
- Linter: ESLint (config: .eslintrc)
- Test framework: Jest + Testing Library
- Min test coverage: 80%
- PR naming: [JIRA-KEY] Short description
- Branch naming: feature/JIRA-KEY-short-description
- Commit style: Conventional Commits
```

##### D. Regulatory & Compliance Requirements

> 🔒 **This section is mandatory.** Every story the agent works on will be checked against these constraints. Any implementation that violates these rules must be blocked before a PR is submitted.

```markdown
## Regulatory Constraints (Always Enforce)

### Data Privacy
- GDPR compliant: no PII stored without explicit consent
- All user data must be encryptable at rest (AES-256)
- Data retention max: 90 days unless exception approved
- No logging of passwords, tokens, or payment data

### Security
- OWASP Top 10 must be addressed in all endpoints
- All API endpoints require authentication (JWT/OAuth2)
- Rate limiting required on all public endpoints
- Input validation on all user-supplied data
- Secrets via environment variables only (never hardcoded)

### Accessibility
- WCAG 2.1 AA compliance for all UI components
- Keyboard navigation support required
- ARIA labels on interactive elements

### Financial / Industry-Specific
- [Add your specific regulatory requirements here]
- e.g., PCI-DSS for payment handling
- e.g., SOC2 controls for enterprise features
```

##### E. Monitoring Requirements

```markdown
## Monitoring (Required for Every Story)
- Every new endpoint: add to Datadog APM
- Every background job: add heartbeat monitor
- Error rate alert threshold: >1% over 5 min
- Latency alert threshold: p99 >500ms
- Log level: INFO for business events, ERROR for failures
- Structured logging format: JSON with trace_id field
- Dashboard: update team dashboard for new features
```

##### F. Testing Requirements

```markdown
## Testing
- Unit tests: all business logic functions
- Integration tests: all API endpoints
- E2E tests: all critical user flows (Playwright)
- Load test: any endpoint expected >100 RPS
- Security scan: run SAST on every PR (Semgrep)
```

##### G. Domain Knowledge

```markdown
## Domain Knowledge
- [Describe your business domain for the agent]
- e.g., "This is a B2B SaaS for logistics companies."
- e.g., "Users are warehouse managers tracking shipments."
- Key entities: Order, Shipment, Warehouse, Driver
- Key business rules: [list critical rules here]
```

### 2.3 Additional Context Files

Place these files at the root of your repository to give the agent richer context:

| File | Purpose |
|------|---------|
| `CLAUDE.md` | Master agent configuration (required) |
| `ARCHITECTURE.md` | System diagrams and design decisions |
| `API_CONTRACTS.md` | API schemas and integration specs |
| `DATA_MODELS.md` | Database schemas and entity relationships |
| `RUNBOOK.md` | Operational procedures for the agent to follow |
| `GLOSSARY.md` | Business and domain terminology definitions |

### 2.4 Initializing the Agent in a Session

Always start a Claude Code session by grounding the agent:

```bash
# Navigate to your project
cd /path/to/your/project

# Start Claude Code — it will auto-read CLAUDE.md
claude

# Verify agent understands the project
> Summarize what you know about this project, its tech stack,
>  and the key regulatory constraints you must follow.
```

> ✅ **Best Practice:** Always verify the agent's understanding before starting any implementation task. If the summary is incomplete, update `CLAUDE.md` and restart the session.

---

## 3. Phase 1 — Strategy & KPIs

Before writing a single line of code, teams define what success looks like. This phase anchors everything that follows.

### 3.1 Defining Objectives

Each initiative must have clearly defined objectives using the OKR format:

- **Objective:** Qualitative, inspiring goal
- **Key Results:** 2–4 measurable outcomes with a target number
- **Owner:** Named individual accountable for the objective
- **Timeline:** Quarter or milestone

### 3.2 Creating KPIs

For each Key Result, define at least one KPI that will be measured in your monitoring system:

| KPI Category | Example KPI | Target | Monitoring Tool |
|-------------|-------------|--------|-----------------|
| Adoption | Daily Active Users | >10,000/day | Datadog / Mixpanel |
| Performance | API p99 Latency | <300ms | Datadog APM |
| Reliability | Error Rate | <0.1% | Datadog Monitors |
| Business | Conversion Rate | >8% | Analytics Platform |
| Quality | Defect Escape Rate | <2% | Jira Metrics |

> 📊 **Monitoring Linkage:** Every KPI defined here must be wired to a dashboard and alert by the time the feature is released. This is enforced in the agent's implementation checklist.

---

## 4. Phase 2 — Roadmap Planning

### 4.1 Roadmap Structure

The roadmap is organized into three horizons:

| Horizon | Timeframe | Certainty | Detail Level |
|---------|-----------|-----------|--------------|
| H1: Now | Current Quarter | High | Epics + Stories defined |
| H2: Next | Next 1–2 Quarters | Medium | Epics defined, stories TBD |
| H3: Later | 6–12 Months | Low | Themes only |

### 4.2 Prioritization Criteria

Use the **RICE framework** for all roadmap items:

- **Reach:** How many users affected per quarter?
- **Impact:** Effect on key metric (3=massive, 2=high, 1=medium, 0.5=low)
- **Confidence:** How sure are we? (100%, 80%, 50%)
- **Effort:** Person-weeks of work required

```
Score = (Reach × Impact × Confidence) / Effort
```

### 4.3 Roadmap → Jira Epics

Every roadmap item in H1 must be converted to a Jira Epic before the sprint starts. The agent can assist with this conversion when provided a roadmap document.

---

## 5. Phase 3 — Requirements & User Story Creation

### 5.1 Requirement Types

Every feature requires three types of requirements before agent implementation begins:

- **Business Requirements (BRD):** What the business needs and why
- **Functional Requirements:** What the system must do
- **Technical Requirements (TRD):** How the system will do it

### 5.2 User Story Format

> 📝 **Standard Format:** As a [user type], I want to [action], so that [benefit]. Every story must have Acceptance Criteria (Given/When/Then), Definition of Done, and Monitoring Criteria.

#### 5.2.1 Story Template

```
Title: [JIRA-KEY] Short, action-oriented title

User Story:
As a [persona], I want to [capability], so that [benefit].

Background:
[Context the developer needs to understand the feature]

Acceptance Criteria:
Given [initial context]
When [action is performed]
Then [expected outcome]
And [additional assertion]

Technical Notes:
- API endpoint: POST /api/v1/[resource]
- Auth required: Yes (JWT)
- Rate limit: 100 req/min per user
- DB changes: [migrations required]

Regulatory Checklist:
[ ] GDPR: Does this handle PII? → Yes/No (justify)
[ ] Security: Auth + input validation confirmed
[ ] Accessibility: WCAG AA for any UI changes
[ ] [Other applicable regulation]

Monitoring Requirements:
- New metric: [metric name, type, tags]
- Alert: [condition] → notify [channel]
- Dashboard: Add to [dashboard name]

Definition of Done:
[ ] Code implemented and peer-reviewed
[ ] Unit tests written (>80% coverage)
[ ] Integration tests passing
[ ] Regulatory checklist signed off
[ ] Monitoring configured and tested
[ ] Documentation updated
```

### 5.3 Automated Story Creation with AI Agent

The agent can automatically create Jira stories from a requirements document, eliminating manual ticket creation and ensuring consistent formatting.

#### 5.3.1 Prerequisites Before Running the Agent

1. Business requirements document written by PM
2. Technical design approved by Tech Lead
3. Regulatory impact assessment completed
4. Agent project configuration (`CLAUDE.md`) is up to date

#### 5.3.2 Triggering Automated Story Creation

```bash
claude

> I have a requirements document for [feature name].
> Please read it and create Jira stories following our
> project template. The Jira project key is [KEY].
> Check each story against our regulatory constraints
> in CLAUDE.md before creating it.
```

#### 5.3.3 What the Agent Does

1. Reads and parses the requirements document
2. Identifies distinct user stories and acceptance criteria
3. Checks each story against regulatory constraints in `CLAUDE.md`
4. Generates story content following the standard template
5. Creates stories in Jira via API
6. Links stories to the parent Epic
7. Sets appropriate labels, priority, and story points estimate
8. Returns a summary with Jira links for PM review

> 👥 **Human Checkpoint:** After automated story creation, the PM and Tech Lead must review each story before it moves to "Ready for Development". The agent creates a draft — humans approve.

---

## 6. Phase 4 — Design & UX

### 6.1 Design Process

Design happens before implementation. The following artifacts must be produced and linked to Jira stories:

| Artifact | Owner | When Required |
|----------|-------|---------------|
| User Flow Diagram | Designer / PM | All user-facing stories |
| Wireframes | Designer | New screens or major layout changes |
| High-Fidelity Mockups | Designer | Customer-facing features |
| Component Spec | Designer + Engineer | New reusable components |
| Accessibility Audit | Designer / QA | All UI changes |

### 6.2 Design-to-Implementation Handoff

Designs must include the following for agent implementation:

- Component names matching the design system
- Exact spacing, color tokens (no raw hex values)
- Responsive breakpoints specified
- Interaction states (hover, active, disabled, error, loading)
- Accessibility notes (ARIA roles, focus order)

> ✅ **Design Link Requirement:** Every Jira story must have a Figma (or equivalent) link attached before moving to "In Development". The agent will reference the design spec during implementation.

---

## 7. Phase 5 — Implementation with AI Agent

### 7.1 Pre-Implementation Checklist

Before the agent starts coding, verify:

- [ ] Story is in "Ready for Development" status in Jira
- [ ] Design artifacts linked to the story
- [ ] Technical requirements documented
- [ ] Regulatory checklist completed on the story
- [ ] No blocking dependencies unresolved
- [ ] `CLAUDE.md` is current and accurate

### 7.2 Agent Workflow: Branch Creation

#### Step 1 — Start the Implementation Session

```bash
claude

> I want to implement story [JIRA-KEY].
> Please:
> 1. Read the story details from Jira
> 2. Confirm you understand the requirements
> 3. Create a feature branch following our naming convention
> 4. Link the branch to the Jira story
> 5. Summarize your implementation plan before starting
```

#### Step 2 — What the Agent Does

1. Fetches the story from Jira using the project key
2. Extracts the story title and key
3. Creates branch: `git checkout -b feature/[JIRA-KEY]-short-description`
4. Pushes the branch to origin
5. Links the branch to the Jira story via the development panel
6. Presents an implementation plan for human confirmation before coding

> 🛑 **Confirmation Gate:** The agent **MUST** present its implementation plan and wait for explicit human approval before writing any code. This prevents wasted effort on misunderstood requirements.

### 7.3 Implementation Standards the Agent Must Follow

#### 7.3.1 Regulatory Compliance Check (Automatic)

Before implementing any feature, the agent automatically checks the story against all constraints in `CLAUDE.md`:

- If PII is involved → validates encryption and consent handling
- If a new API endpoint → validates auth, rate limiting, input validation
- If UI changes → validates WCAG 2.1 AA compliance
- If payment data → validates PCI-DSS controls

If any constraint is violated, the agent **STOPS** and reports to the developer before proceeding.

#### 7.3.2 Monitoring Implementation (Mandatory)

> 📊 **Monitoring is not optional.** The agent is instructed to treat monitoring setup as part of the feature implementation, not as an afterthought. A PR without monitoring code will not pass review.

For every story, the agent must implement:

| Monitoring Type | What the Agent Implements |
|----------------|--------------------------|
| Structured Logging | JSON logs with trace_id, user_id, action, duration |
| Metrics | Custom counters/gauges for business events |
| Distributed Tracing | Spans for all cross-service calls |
| Health Checks | Endpoint or heartbeat for new services |
| Alerting Config | Alert rules for error rate, latency, and business KPIs |
| Dashboard Update | New panels added to team dashboard |

**Example — Agent-Generated Monitoring Code:**

```python
# Structured logging
logger.info(
    "user_action",
    extra={
        "trace_id": get_trace_id(),
        "user_id": current_user.id,
        "action": "shipment.created",
        "shipment_id": shipment.id,
        "duration_ms": elapsed,
    }
)

# Metric instrumentation
statsd.increment("shipment.created", tags=["team:logistics"])
statsd.histogram("shipment.create.duration", elapsed)
```

#### 7.3.3 Code Quality Standards

The agent enforces these standards on every file it touches:

- Runs linter and formatter before committing
- Writes unit tests alongside implementation code
- Keeps functions under 40 lines (refactors if needed)
- Adds docstrings/JSDoc to all public functions
- No hardcoded secrets or configuration values
- Handles error cases explicitly (no silent failures)

### 7.4 Agent Workflow: Pull Request Creation

#### Step 1 — Pre-PR Self-Check

```bash
claude

> Implementation is complete. Please perform a pre-PR self-check:
> 1. Verify all acceptance criteria are met
> 2. Confirm regulatory checklist is satisfied
> 3. Confirm monitoring is implemented
> 4. Run linter and tests
> 5. Create a PR with the standard template
```

#### Step 2 — PR Template (Agent-Generated)

```markdown
## [JIRA-KEY] Story Title

### Jira Story
[Link to Jira story]

### Summary
[What was implemented and why]

### Changes
- [File changed]: [What was changed and why]

### Regulatory Compliance
- [ ] GDPR: [Addressed / Not Applicable — reason]
- [ ] Security: Auth + validation implemented
- [ ] Accessibility: WCAG AA verified

### Monitoring
- [ ] Logging added: [list log events]
- [ ] Metrics added: [list metric names]
- [ ] Alerts configured: [describe alerts]
- [ ] Dashboard updated: [link]

### Testing
- [ ] Unit tests: X new tests, Y% coverage
- [ ] Integration tests: [describe]
- [ ] Manual test steps: [describe how reviewer can test]

### Screenshots / Recordings
[For UI changes — before/after screenshots]
```

#### Step 3 — PR Submission Steps

1. Agent pushes final commit
2. Agent creates PR via GitHub/GitLab API
3. Agent assigns reviewers from `CLAUDE.md` reviewer list
4. Agent links PR back to the Jira story
5. Agent moves Jira story to "In Review" status
6. Agent posts PR link to team Slack channel

---

## 8. Phase 6 — Code Review (Human)

Code review is a human responsibility. AI agents assist in creating clean, well-structured PRs, but the review decision belongs to qualified engineers.

### 8.1 Reviewer Responsibilities

| Review Area | What to Check |
|------------|---------------|
| Correctness | Does the code do what the story requires? |
| Regulatory | Are all compliance checklist items genuinely satisfied? |
| Security | Are there any vulnerabilities (injection, auth bypass, etc.)? |
| Monitoring | Are logs, metrics, and alerts meaningful and correct? |
| Architecture | Does it fit the overall system design? |
| Performance | Any obvious N+1 queries, missing indexes, or bottlenecks? |
| Tests | Are tests meaningful and edge cases covered? |
| Documentation | Are public APIs and complex logic documented? |

### 8.2 Review SLA

- **Standard PRs:** reviewed within 1 business day
- **Hotfix PRs:** reviewed within 2 hours
- **Large PRs (>400 lines):** may request splitting

### 8.3 Approval Criteria

A PR requires:

- Minimum 1 approval from a senior engineer or tech lead
- All CI checks passing (tests, lint, security scan, coverage)
- No unresolved reviewer comments
- Regulatory checklist signed off by tech lead

> 🔒 If the reviewer finds that the agent's regulatory compliance section was incorrectly marked, they must **reject the PR** and update `CLAUDE.md` to prevent the same mistake in future stories.

---

## 9. Phase 7 — Testing & QA (Human)

Testing is a human responsibility that validates what the agent implemented against real-world behavior. Automated tests (unit, integration) are written by the agent; functional and exploratory testing is done by humans.

### 9.1 Testing Responsibilities

| Test Type | Who | When | Tool |
|-----------|-----|------|------|
| Unit Tests | Agent (auto) | During implementation | Jest / pytest |
| Integration Tests | Agent (auto) | During implementation | Supertest / httpx |
| Functional QA | Human QA | After PR merge to staging | Manual + Jira |
| Exploratory Testing | Human QA | After functional QA | Manual |
| Performance Testing | Engineer | For high-traffic features | k6 / Locust |
| Security Testing | Engineer / Security | Before major releases | DAST / Pen test |
| UAT | Product Manager | Before production release | Manual |

### 9.2 Functional QA Process

1. Deploy to staging environment
2. QA verifies each acceptance criterion from the Jira story
3. QA checks monitoring — do logs appear? Do metrics increment?
4. QA validates regulatory requirements (e.g., test GDPR data handling)
5. QA documents findings in Jira story comments
6. QA marks story as "QA Passed" or returns to "In Development" with findings

### 9.3 QA Monitoring Validation

> 📊 **QA must explicitly verify monitoring during testing**, not just functional behavior. Check that:
> 1. Expected log events appear in the logging system
> 2. Metrics are visible in the monitoring dashboard
> 3. Test alerts fire correctly when thresholds are breached

---

## 10. Phase 8 — Release & Monitoring

### 10.1 Release Checklist

Before any feature goes to production:

- [ ] QA sign-off obtained
- [ ] PM UAT sign-off obtained
- [ ] Monitoring dashboard reviewed — all panels working
- [ ] Alerts configured and tested in staging
- [ ] Rollback plan documented
- [ ] Feature flag configured (if applicable)
- [ ] On-call engineer notified of the release
- [ ] Release notes prepared

### 10.2 Deployment Strategy

| Feature Type | Deployment Strategy | Rollback Time |
|-------------|--------------------|--------------| 
| Small / Low Risk | Direct deploy + monitor 30 min | < 5 minutes |
| Medium / New Feature | Canary: 5% → 25% → 100% | < 2 minutes |
| High Risk / Critical Path | Blue/Green deployment | < 1 minute |
| Experimental | Feature flag (0–100% rollout) | Instant (flag off) |

### 10.3 Post-Release Monitoring Protocol

**Immediate (0–30 minutes after release)**
1. Engineer watches error rate dashboard
2. Verify all new metrics are flowing
3. Confirm no spike in latency
4. Check that new alerts are active

**Short-term (24 hours after release)**
1. Review error logs for unexpected patterns
2. Check business KPI metrics are moving as expected
3. Validate user-facing behavior matches acceptance criteria

**Ongoing**
1. Weekly review of KPIs against targets defined in Phase 1
2. Monthly capacity planning review
3. Quarterly retrospective on feature impact vs. original hypothesis

### 10.4 Monitoring Stack Requirements

> 🔒 Every team using this process must have a monitoring stack configured before they can release to production.

| Layer | Minimum Requirement | Recommended Tool |
|-------|--------------------|-----------------| 
| Logging | Structured JSON logs with trace IDs | Datadog Logs / ELK Stack |
| Metrics | Custom business + system metrics | Datadog / Prometheus |
| Tracing | Distributed traces across services | Datadog APM / Jaeger |
| Alerting | Error rate + latency + business KPIs | PagerDuty / Opsgenie |
| Dashboards | Team dashboard per service | Datadog / Grafana |
| Uptime | Synthetic monitors for critical flows | Datadog Synthetics |

---

## 11. Regulatory Compliance Framework

Regulatory compliance is baked into every step of this process. The agent is constrained by the rules in `CLAUDE.md` and cannot bypass them.

### 11.1 Compliance Layers

| Layer | Mechanism | Enforced By |
|-------|-----------|-------------|
| Agent Config | `CLAUDE.md` constraints section | Agent auto-check per story |
| Story Template | Regulatory checklist on every story | PM + Tech Lead review |
| PR Template | Compliance section in every PR | Code reviewer sign-off |
| CI/CD Pipeline | SAST, secrets scan, dependency audit | Automated gates |
| QA Testing | Explicit compliance test cases | QA team validation |
| Post-Release | Audit logging, monitoring alerts | On-call + Security team |

### 11.2 Adding New Regulatory Requirements

When new regulations apply (e.g., a new privacy law, a new security standard):

1. Security/Legal team defines the new constraint in plain English
2. Tech Lead translates to a technical rule and adds to `CLAUDE.md`
3. The change is announced to all teams
4. All in-flight stories are re-evaluated against the new rule
5. `CLAUDE.md` change is version-controlled and auditable

### 11.3 Compliance Audit Trail

The following artifacts form the compliance audit trail for each feature:

- Jira story with completed regulatory checklist
- PR with compliance section signed off by reviewer
- CI/CD logs showing security scan results
- QA sign-off document
- Monitoring data from post-release period

---

## 12. Roles & Responsibilities

| Role | Responsibilities | Agent Interaction |
|------|-----------------|-------------------|
| Product Manager | KPIs, roadmap, requirements, UAT, release sign-off | Provides requirements to agent; reviews agent-created stories |
| Tech Lead | Architecture, TRD, `CLAUDE.md` owner, PR final approval | Maintains agent configuration; approves implementation plans |
| Engineer | Implementation, unit/integration tests, monitoring | Guides agent; reviews agent output; runs pre-PR checks |
| Designer | UX flows, mockups, accessibility, handoff specs | Provides design context to agent via Figma links |
| QA Engineer | Functional testing, exploratory testing, QA sign-off | Validates agent-implemented features; checks monitoring |
| Security / Legal | Regulatory rules, compliance review, audits | Defines constraints that go into `CLAUDE.md` |

---

## 13. End-to-End Process Flow

```
┌─────────────────────────────────────────────────────────┐
│  1. STRATEGY & KPIs                                     │
│     PM defines OKRs and KPIs linked to monitoring       │
└────────────────────────┬────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────┐
│  2. ROADMAP                                             │
│     PM prioritizes initiatives using RICE; creates Epics│
└────────────────────────┬────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────┐
│  3. REQUIREMENTS                                        │
│     PM + TL write BRD + TRD                             │
│     → Agent auto-creates Jira stories                   │
└────────────────────────┬────────────────────────────────┘
                         │ 👥 Human Checkpoint: PM + TL review stories
┌────────────────────────▼────────────────────────────────┐
│  4. DESIGN & UX                                         │
│     Designer produces mockups; links to Jira stories    │
└────────────────────────┬────────────────────────────────┘
                         │ 👥 Human Checkpoint: Design review
┌────────────────────────▼────────────────────────────────┐
│  5. IMPLEMENTATION                                      │
│     Agent reads story → creates branch                  │
│     → implements → creates PR                           │
└────────────────────────┬────────────────────────────────┘
                         │ 👥 Human Checkpoint: Engineer confirms plan
┌────────────────────────▼────────────────────────────────┐
│  6. CODE REVIEW                                         │
│     Human engineer reviews PR                           │
│     → approves or requests changes                      │
└────────────────────────┬────────────────────────────────┘
                         │ 👥 Human Checkpoint: PR approval required
┌────────────────────────▼────────────────────────────────┐
│  7. TESTING & QA                                        │
│     Human QA validates in staging                       │
│     → verifies monitoring → signs off                   │
└────────────────────────┬────────────────────────────────┘
                         │ 👥 Human Checkpoint: QA + PM sign-off required
┌────────────────────────▼────────────────────────────────┐
│  8. RELEASE & MONITOR                                   │
│     Deploy → watch monitoring → validate KPIs           │
│     → close Jira story                                  │
└─────────────────────────────────────────────────────────┘
```

### Human Checkpoints Summary

| Checkpoint | Who Signs Off | Blocking? |
|-----------|---------------|-----------|
| Story approval | PM + Tech Lead | Yes — agent cannot start without this |
| Design review | Design lead + PM | Yes — no implementation without approved design |
| Implementation plan | Engineer | Yes — agent waits for explicit go-ahead |
| PR approval | Senior Engineer / Tech Lead | Yes — CI enforces approval requirement |
| QA sign-off | QA Engineer | Yes — no production deploy without this |
| UAT sign-off | Product Manager | Yes — final gate before production |

---

## 14. Quick Reference — Agent Commands

### 14.1 Common Agent Prompts

| Task | Agent Command |
|------|--------------|
| Verify project understanding | `Summarize this project's tech stack and key regulatory constraints from CLAUDE.md` |
| Create Jira stories | `Read [requirements doc] and create Jira stories for project [KEY] using our template` |
| Start a story | `Implement story [JIRA-KEY]. Read the Jira details, create a branch, and present your plan.` |
| Compliance check | `Before implementing [JIRA-KEY], check all regulatory constraints in CLAUDE.md and report any risks` |
| Create a PR | `Implementation complete for [JIRA-KEY]. Run checks and create a PR following our template.` |
| Add monitoring | `Review the monitoring for [JIRA-KEY] and ensure all metrics, logs, and alerts are implemented` |
| Update CLAUDE.md | `A new regulation requires [X]. Update CLAUDE.md constraints section accordingly.` |

### 14.2 Escalation Matrix

| Situation | Escalate To | SLA |
|-----------|-------------|-----|
| Agent cannot implement due to regulatory conflict | Tech Lead + Security | Same day |
| PR rejected for compliance reasons | Tech Lead + PM | Same day |
| Monitoring alerts firing post-release | On-call Engineer | Immediate |
| Story requirements unclear | PM | Before development starts |
| `CLAUDE.md` update needed | Tech Lead | Within 1 business day |

### 14.3 Definition of "Ready for Development"

A story is only ready for the agent to implement when **all** of the following are true:

- [ ] Story written in standard template format
- [ ] Acceptance criteria are clear and testable
- [ ] Regulatory checklist completed
- [ ] Monitoring requirements documented
- [ ] Design artifacts linked (for UI stories)
- [ ] Technical notes include API contracts and DB changes
- [ ] No unresolved dependencies or blockers
- [ ] Approved by PM and Tech Lead

---

*AI-Powered Product Engineering Process Framework — Version 1.0 · 2025*
*Confidential — Internal Use Only*