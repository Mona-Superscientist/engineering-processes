# AI Agent Playbook — Zero to Production

> **A complete worked example:** Building a real feature end-to-end using AI agents at every step.
> Stack: Flutter · Node.js + TypeScript · React + TypeScript · Notion · Figma · Jira

---

## The Example Feature

**"Smart Reorder Alerts"** — a feature for a B2B inventory management app that automatically detects when stock is running low and sends push notifications to warehouse managers, with a mobile dashboard to manage alert thresholds.

This example is deliberately realistic: it touches mobile (Flutter), backend (Node/TS), frontend (React/TS), and requires monitoring and a safe deployment. Follow it step by step and substitute your own feature details.

---

## Table of Contents

1. [Setup — Configure Your Agents](#1-setup--configure-your-agents)
2. [Product Ideation & KPIs](#2-product-ideation--kpis)
3. [Roadmap in Notion](#3-roadmap-in-notion)
4. [UX Design with Figma](#4-ux-design-with-figma)
5. [Jira Stories — Agent Auto-Creates Tickets](#5-jira-stories--agent-auto-creates-tickets)
6. [Backend Implementation — Node + TypeScript](#6-backend-implementation--node--typescript)
7. [Frontend Implementation — React + TypeScript](#7-frontend-implementation--react--typescript)
8. [Mobile Implementation — Flutter](#8-mobile-implementation--flutter)
9. [Testing](#9-testing)
10. [Monitoring Setup](#10-monitoring-setup)
11. [Blue-Green Deployment](#11-blue-green-deployment)
12. [Go Live Checklist](#12-go-live-checklist)
13. [Cost Summary](#13-cost-summary)

---

## 1. Setup — Configure Your Agents

Before anything else, one-time setup across your tools.

### 1.1 CLAUDE.md — Root of Every Repo

Create this file at the root of **each** repository. The agent reads it at the start of every session.

```markdown
## Project
Name: InventoryOS
Type: B2B SaaS — Inventory Management
Jira Project Key: INV
Notion Workspace: https://notion.so/your-workspace
Figma Team: https://figma.com/files/team/your-team-id

## Stack
Mobile: Flutter 3.x (Dart)
Backend: Node.js 20 + TypeScript 5, Express, Prisma ORM
Frontend: React 18 + TypeScript 5, Vite, TanStack Query
Database: PostgreSQL 15 (primary), Redis 7 (cache/queues)
Push Notifications: Firebase Cloud Messaging (FCM)
CI/CD: GitHub Actions
Cloud: AWS (ECS Fargate, RDS, ElastiCache, ALB)
Monitoring: Datadog (APM + Logs + Dashboards)

## Code Standards
- Branch naming: feature/INV-{number}-short-description
- Commit style: Conventional Commits (feat:, fix:, chore:)
- PR naming: [INV-{number}] Short title
- Backend test framework: Jest + Supertest
- Frontend test framework: Vitest + Testing Library
- Mobile test framework: Flutter test + integration_test
- Min coverage: 80%
- Linter: ESLint (backend/frontend), dart analyze (mobile)

## Regulatory Constraints (Always Enforce)
- No PII in logs (mask email, phone, user IDs in log output)
- All API endpoints require JWT auth (except /health)
- Rate limit all endpoints: 100 req/min per user default
- Input validation via Zod (backend) on all request bodies
- WCAG 2.1 AA for all React UI

## Monitoring (Required on Every Story)
- New endpoints → register in Datadog APM
- Business events → structured JSON logs with trace_id
- New background jobs → heartbeat monitor
- Alert thresholds: error rate >1% / p99 latency >500ms
- Update team Datadog dashboard for every feature
```

### 1.2 Connect Claude Code to Jira and Notion

```bash
# Install Claude Code
npm install -g @anthropic-ai/claude-code

# Set your API keys (add to shell profile)
export ANTHROPIC_API_KEY=sk-ant-...
export JIRA_API_TOKEN=your-jira-token
export JIRA_BASE_URL=https://your-org.atlassian.net
export JIRA_EMAIL=you@yourcompany.com
export NOTION_API_KEY=secret_...
export NOTION_ROADMAP_DB_ID=your-database-id
```

### 1.3 Verify Agent Understands Your Project

```bash
cd /path/to/backend-repo
claude

> Read CLAUDE.md and summarize: our stack, branch naming
> convention, and the top 3 regulatory constraints.
```

Expected output: The agent should accurately describe Node/TS backend, `feature/INV-{n}-...` branches, and call out JWT auth, no-PII logging, and Zod validation. If anything is wrong, fix `CLAUDE.md` before proceeding.

---

## 2. Product Ideation & KPIs

**Time investment:** ~2 hours human time, agent handles research and structuring.

### 2.1 Use the Agent to Validate the Idea

Before writing a single requirement, ask the agent to stress-test the idea:

```bash
claude

> We want to build "Smart Reorder Alerts" — auto-detect low stock
> and push notify warehouse managers. Our users are B2B warehouse
> managers, typically 35-55 years old, not highly technical.
>
> Please:
> 1. Identify the top 3 risks with this feature
> 2. Suggest 5 clarifying questions the PM should answer
>    before we invest in this
> 3. Propose a lean MVP scope (what's the smallest version
>    that delivers real value)
> 4. Draft KPIs that could be measured in Datadog and our
>    analytics platform
```

Agent output (example):

```
Risks:
1. Alert fatigue — too many notifications → users disable them
2. Threshold calibration — wrong defaults cause false positives
3. FCM delivery reliability — push may not reach all devices

Clarifying questions for PM:
1. Who sets thresholds — the manager or system auto-learns?
2. What's the notification channel (push only, or email/SMS too)?
3. How do we handle items with seasonal demand?
4. Can managers snooze or dismiss alerts?
5. Is this per-warehouse or org-wide?

MVP scope:
- Manual threshold per SKU (no ML yet)
- Push notification via FCM when stock < threshold
- Mobile screen to view and edit thresholds
- Simple reorder count badge on home screen

KPIs:
- alert_triggered (counter, tags: warehouse_id, sku_id)
- alert_dismissed (counter — signals false positives)
- reorder_completed_after_alert (counter — success metric)
- threshold_updated (counter — engagement signal)
- notification_delivery_rate (gauge, per FCM delivery receipt)
```

### 2.2 Define KPIs in Notion

```bash
claude

> Create a KPI page in our Notion workspace for the
> "Smart Reorder Alerts" feature. Use this structure:
>
> - Feature name and one-line description
> - Business objective (reduce stockouts by 30%)
> - Success KPIs table with: KPI name, target, measurement
>   method, owner, review cadence
> - Counter-metrics (things we don't want to go up)
> - How each KPI maps to a Datadog metric name
>
> Use the KPIs we just defined. Create it in the
> Product KPIs database (ID: $NOTION_ROADMAP_DB_ID).
```

> 💡 **Cost tip:** Do this in one agent session. Don't close and restart — the agent loses context and you pay for re-loading it.

### 2.3 Final KPI Table

| KPI | Target | Datadog Metric | Review |
|-----|--------|----------------|--------|
| Alert delivery rate | >98% | `reorder.alert.delivery_rate` | Weekly |
| False positive rate (dismissals) | <15% | `reorder.alert.dismissed / triggered` | Weekly |
| Reorder completion rate | >60% of alerts lead to reorder | `reorder.completed_after_alert` | Monthly |
| Threshold adoption | >80% of warehouses set at least 1 threshold | `reorder.threshold.configured` | Monthly |
| Push notification p99 latency | <3s from trigger to delivery | `reorder.notification.latency_p99` | Weekly |

---

## 3. Roadmap in Notion

**Time investment:** 30 minutes human time.

### 3.1 Agent Creates the Roadmap Entry

```bash
claude

> Add "Smart Reorder Alerts" to our Notion roadmap database.
> 
> Properties to set:
> - Name: Smart Reorder Alerts
> - Status: In Discovery
> - Horizon: H1 (Current Quarter)
> - RICE Score: calculate from these inputs:
>     Reach: 2,400 users/quarter
>     Impact: 2 (high — directly reduces stockouts)
>     Confidence: 80%
>     Effort: 3 person-weeks
> - Epic Jira Key: (create the Epic first, then link it)
> - KPI Page: [link to page created in step 2]
> - Owner: @ProductManager
> - Target release: end of sprint 6
>
> Then create a Jira Epic: "Smart Reorder Alerts"
> in project INV and paste the Epic link back into Notion.
```

Agent creates:
- Notion roadmap entry with RICE = (2400 × 2 × 0.8) / 3 = **1,280** (high priority)
- Jira Epic `INV-42: Smart Reorder Alerts`
- Cross-links both

### 3.2 Roadmap Entry Result

```
Feature: Smart Reorder Alerts
RICE Score: 1,280
Horizon: H1 — Current Quarter
Epic: INV-42
Status: In Discovery → moves to In Development after design review
Sprint Target: Sprint 6 (3 weeks from now)
```

---

## 4. UX Design with Figma

**Time investment:** 3–4 hours human designer time + agent for spec writing.

### 4.1 What the Agent Can Do in Figma

The agent cannot draw in Figma directly, but it can:
- Generate a complete component specification the designer follows
- Write interaction descriptions for each state
- Produce the accessibility annotation text
- Create the Figma file structure instructions
- After design is done, extract the spec and write it into Jira stories

### 4.2 Agent Generates the Design Brief

```bash
claude

> Write a detailed UX design brief for "Smart Reorder Alerts".
> Our users are warehouse managers (non-technical, mobile-first).
>
> Cover:
> 1. Screens required (list all, with purpose)
> 2. For each screen: key components, user actions, empty states,
>    error states, loading states
> 3. Mobile-specific considerations (offline mode? one-thumb use?)
> 4. Notification design (what the push notification looks like)
> 5. Accessibility requirements (WCAG AA for the React web version)
> 6. Design tokens to use from our existing system
>
> Format this as a Figma-ready brief the designer can
> execute without further clarification.
```

Example output (abbreviated):

```
SCREENS REQUIRED

1. Alert Threshold Management (Mobile — Flutter)
   Purpose: View and edit reorder thresholds per SKU
   Components:
   - Search bar (filter by SKU name or code)
   - SKU list item: name, current stock, threshold, status badge
   - Threshold edit bottom sheet: numeric input, unit label, save CTA
   States:
   - Loading: skeleton rows (8 items)
   - Empty: "No items configured — tap + to add your first threshold"
   - Error: "Couldn't load items. Pull to refresh."
   - Editing: bottom sheet slides up, keyboard visible, save disabled
     until value changed
   Accessibility: min touch target 48×48dp, labels on all inputs

2. Alert Inbox (Mobile — Flutter)
   Purpose: View triggered alerts and take action
   Components:
   - Filter chips: All / Active / Dismissed
   - Alert card: SKU name, warehouse, triggered time, stock level,
     threshold, "Mark as Ordered" CTA, "Dismiss" secondary action
   States:
   - Empty active: "You're all caught up 🎉"
   - Loading: 3 skeleton cards

3. Alert Management Dashboard (Web — React)
   Purpose: Ops/admin view across all warehouses
   Components:
   - Summary cards: Active alerts, Dismissal rate, Reorder rate
   - Data table: filterable by warehouse, SKU, date range
   - Bulk actions: dismiss, export CSV
   Accessibility: full keyboard nav, ARIA live regions for real-time updates

PUSH NOTIFICATION DESIGN
Title: "⚠️ Low Stock: {SKU Name}"
Body: "{warehouse_name} — {current_stock} {unit} remaining
       (threshold: {threshold} {unit})"
Action buttons: "View" | "Dismiss"
```

### 4.3 After Designer Completes Figma

Once the designer has built screens in Figma:

```bash
claude

> The designer has shared this Figma link: https://figma.com/...
> 
> Please:
> 1. Read the design and extract component specs for each screen
> 2. Note any missing states or accessibility gaps
> 3. Add the Figma link to Jira Epic INV-42
> 4. Create a design-to-implementation checklist as a
>    Notion page linked to the Epic
```

---

## 5. Jira Stories — Agent Auto-Creates Tickets

**Time investment:** 20 minutes human review.

### 5.1 Agent Creates All Stories

```bash
claude

> Based on our design brief and MVP scope for Smart Reorder Alerts,
> create Jira stories under Epic INV-42.
>
> For each story:
> - Follow our story template from CLAUDE.md
> - Include acceptance criteria (Given/When/Then)
> - Add regulatory checklist
> - Add monitoring requirements
> - Estimate story points (1/2/3/5/8 Fibonacci)
> - Set component label: backend / frontend / mobile
> - Link to Figma screen (use the link from step 4)
>
> Create these stories:
> 1. [Backend] Reorder threshold data model and CRUD API
> 2. [Backend] Stock level monitor background job
> 3. [Backend] FCM push notification service
> 4. [Mobile] Threshold management screen
> 5. [Mobile] Alert inbox screen
> 6. [Frontend] Alert management dashboard
> 7. [Backend] Alert analytics endpoints
> 8. [All] Integration and E2E tests
```

### 5.2 Example Story the Agent Creates

**INV-43: [Backend] Reorder threshold CRUD API**

```
User Story:
As a warehouse manager, I want to set a reorder threshold
for each SKU so that I receive alerts when stock falls below it.

Background:
Thresholds are per-SKU per-warehouse. A warehouse can have
multiple SKUs, each with an independent threshold. Thresholds
are a positive integer representing units.

Acceptance Criteria:
Given I am authenticated as a warehouse manager
When I POST /api/v1/thresholds with { skuId, warehouseId, quantity }
Then the threshold is saved and I receive 201 with the created resource

Given a threshold exists for SKU X in warehouse Y
When I PUT /api/v1/thresholds/:id with a new quantity
Then the threshold is updated and 200 is returned

Given a threshold exists
When I DELETE /api/v1/thresholds/:id
Then the threshold is removed and no further alerts trigger for that SKU

Technical Notes:
- New table: reorder_thresholds (id, sku_id, warehouse_id,
  quantity, created_by, created_at, updated_at)
- Prisma migration required
- Validate: skuId and warehouseId must exist (FK constraints)
- Validate: quantity > 0 (Zod)
- Auth: JWT required, scope to user's warehouses only

Regulatory Checklist:
[x] No PII in this endpoint — SKU/warehouse IDs only
[x] JWT auth required — enforced via middleware
[x] Zod validation on request body
[ ] Rate limit: inherits global 100 req/min

Monitoring:
- Log: threshold.created, threshold.updated, threshold.deleted
  (include sku_id, warehouse_id, quantity — no PII)
- Metric: reorder.threshold.created (counter)
- No new alert needed (CRUD only, not a critical path)

Story Points: 3
Component: backend
Figma: [Link to Threshold Management screen]
```

### 5.3 Human Review Step

The PM and Tech Lead review each story in Jira:
- Acceptance criteria are testable? ✅
- Regulatory checklist complete? ✅
- Points feel right? ✅
- Move to **Ready for Development**

---

## 6. Backend Implementation — Node + TypeScript

**Time investment:** Engineer supervises, agent codes.

### 6.1 Start with Story INV-43

```bash
cd backend-repo
claude

> I want to implement INV-43 (Reorder threshold CRUD API).
> 
> Before coding:
> 1. Fetch the story from Jira (key: INV-43)
> 2. Confirm you understand all acceptance criteria
> 3. Check our CLAUDE.md constraints — flag any risks
> 4. Create the feature branch
> 5. Show me your implementation plan (files you'll create/modify)
>    and WAIT for my approval before writing any code
```

Agent responds:

```
Story fetched: INV-43 — [Backend] Reorder threshold CRUD API

Understanding confirmed:
- CRUD endpoints for reorder thresholds
- Per SKU per warehouse
- JWT protected, scoped to user's warehouses

Regulatory check — all clear:
✅ No PII handled
✅ JWT middleware already exists (src/middleware/auth.ts)
✅ Zod validation pattern exists in other routes
✅ Rate limiting is applied globally

Branch created: feature/INV-43-threshold-crud-api

Implementation plan:
1. prisma/migrations/TIMESTAMP_add_reorder_thresholds.sql
   - New table: reorder_thresholds
2. prisma/schema.prisma
   - Add ReorderThreshold model
3. src/modules/thresholds/threshold.schema.ts
   - Zod schemas for CreateThreshold, UpdateThreshold
4. src/modules/thresholds/threshold.service.ts
   - create(), findAll(), update(), remove()
   - Scope queries to user's warehouse IDs
5. src/modules/thresholds/threshold.controller.ts
   - POST, GET, PUT, DELETE route handlers
6. src/modules/thresholds/threshold.routes.ts
   - Wire routes + auth middleware
7. src/app.ts
   - Register /api/v1/thresholds router
8. src/modules/thresholds/__tests__/threshold.test.ts
   - Unit tests for service layer
9. src/modules/thresholds/__tests__/threshold.integration.test.ts
   - Integration tests for all endpoints

Awaiting your approval to proceed.
```

Engineer reviews the plan → types **"approved, proceed"**

### 6.2 Agent Implements — Key Patterns

The agent produces code following your standards. Spot-check these patterns:

**Prisma Schema Addition**
```typescript
// prisma/schema.prisma
model ReorderThreshold {
  id          String   @id @default(cuid())
  skuId       String
  warehouseId String
  quantity    Int
  createdBy   String
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  sku         Sku       @relation(fields: [skuId], references: [id])
  warehouse   Warehouse @relation(fields: [warehouseId], references: [id])
  createdByUser User    @relation(fields: [createdBy], references: [id])

  @@unique([skuId, warehouseId])
  @@index([warehouseId])
}
```

**Zod Validation**
```typescript
// src/modules/thresholds/threshold.schema.ts
import { z } from 'zod';

export const CreateThresholdSchema = z.object({
  skuId: z.string().cuid(),
  warehouseId: z.string().cuid(),
  quantity: z.number().int().positive(),
});

export const UpdateThresholdSchema = z.object({
  quantity: z.number().int().positive(),
});

export type CreateThresholdInput = z.infer<typeof CreateThresholdSchema>;
export type UpdateThresholdInput = z.infer<typeof UpdateThresholdSchema>;
```

**Service with Monitoring**
```typescript
// src/modules/thresholds/threshold.service.ts
import { logger } from '../../lib/logger';
import { metrics } from '../../lib/metrics';

export class ThresholdService {
  async create(input: CreateThresholdInput, userId: string) {
    const threshold = await prisma.reorderThreshold.create({
      data: { ...input, createdBy: userId },
    });

    logger.info('threshold.created', {
      skuId: threshold.skuId,
      warehouseId: threshold.warehouseId,
      quantity: threshold.quantity,
      // ✅ No PII — no userId in logs per CLAUDE.md
    });
    metrics.increment('reorder.threshold.created');

    return threshold;
  }
  // ... update, remove, findAll
}
```

### 6.3 Agent Creates the PR

```bash
claude

> Implementation of INV-43 is complete.
> 
> 1. Run the linter and tests — fix any failures
> 2. Check all acceptance criteria from INV-43 are met
> 3. Verify regulatory checklist is satisfied
> 4. Create a PR with our template
> 5. Move INV-43 to "In Review" in Jira
> 6. Post the PR link to #team-inventory on Slack
```

Agent:
- Runs `npm run lint && npm run test` — fixes any issues
- Creates PR on GitHub with the template filled in
- Updates Jira status
- Posts to Slack

---

## 7. Frontend Implementation — React + TypeScript

### 7.1 Start the Dashboard Story (INV-48)

```bash
cd frontend-repo
claude

> Implement INV-48: Alert Management Dashboard (React).
> Fetch the story from Jira, create the branch, plan first.
>
> Additional context:
> - Use TanStack Query for data fetching
> - Use our existing DataTable component from src/components/ui/
> - The API endpoints are documented in backend-repo/API_CONTRACTS.md
> - Figma link is attached to the Jira story
```

### 7.2 Key Frontend Patterns the Agent Follows

**API Hook with TanStack Query**
```typescript
// src/features/alerts/hooks/useAlerts.ts
import { useQuery } from '@tanstack/react-query';
import { alertsApi } from '../api/alerts.api';

export function useAlerts(filters: AlertFilters) {
  return useQuery({
    queryKey: ['alerts', filters],
    queryFn: () => alertsApi.getAlerts(filters),
    staleTime: 30_000, // 30s — alerts update frequently
  });
}
```

**Component with Accessibility**
```typescript
// src/features/alerts/components/AlertSummaryCard.tsx
export function AlertSummaryCard({ title, value, trend }: Props) {
  return (
    <article
      aria-label={`${title}: ${value}`}
      className="summary-card"
    >
      <h3 className="summary-card__title">{title}</h3>
      <p
        className="summary-card__value"
        aria-live="polite" // ✅ WCAG: announces updates to screen readers
      >
        {value}
      </p>
      <span
        aria-label={`Trend: ${trend > 0 ? 'up' : 'down'} ${Math.abs(trend)}%`}
      >
        {trend > 0 ? '↑' : '↓'} {Math.abs(trend)}%
      </span>
    </article>
  );
}
```

---

## 8. Mobile Implementation — Flutter

### 8.1 Start Threshold Screen (INV-46)

```bash
cd mobile-repo
claude

> Implement INV-46: Threshold Management Screen (Flutter).
> Fetch from Jira, create branch, plan first.
>
> Context:
> - We use Riverpod for state management
> - API client is in lib/services/api_client.dart
> - Design tokens are in lib/theme/app_theme.dart
> - Target: iOS 14+ and Android 8+
```

### 8.2 Key Flutter Patterns

**Riverpod Provider**
```dart
// lib/features/thresholds/providers/threshold_provider.dart
final thresholdListProvider = AsyncNotifierProvider
    .autoDispose<ThresholdNotifier, List<Threshold>>(() {
  return ThresholdNotifier();
});

class ThresholdNotifier extends AutoDisposeAsyncNotifier<List<Threshold>> {
  @override
  Future<List<Threshold>> build() async {
    return ref.read(thresholdServiceProvider).getThresholds();
  }

  Future<void> updateThreshold(String id, int quantity) async {
    await ref.read(thresholdServiceProvider).update(id, quantity);
    ref.invalidateSelf(); // refetch after update
  }
}
```

**Accessible Widget**
```dart
// lib/features/thresholds/widgets/sku_threshold_tile.dart
class SkuThresholdTile extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Semantics(
      label: '${sku.name}, threshold: $quantity ${sku.unit}',
      button: true,
      child: ListTile(
        title: Text(sku.name),
        subtitle: Text('${currentStock} / ${quantity} ${sku.unit}'),
        trailing: StatusBadge(status: alertStatus),
        onTap: () => _openEditSheet(context),
        // ✅ min touch target enforced by ListTile (48dp)
      ),
    );
  }
}
```

**Agent Creates PR for Mobile**
```bash
claude

> INV-46 implementation is done.
> Run: flutter analyze && flutter test
> Fix any issues, then create the PR and update Jira.
```

---

## 9. Testing

**Time investment:** Human QA owns functional and exploratory. Agent owns test code.

### 9.1 Agent Writes Tests During Implementation

The agent writes tests **as part of implementation**, not after. Remind it if it tries to separate them:

```bash
claude

> You're about to implement the stock monitor background job.
> Write the unit tests FIRST (TDD), then implement the service
> to make them pass. Don't create the PR until coverage is >80%.
```

### 9.2 Backend Test Example (Agent-Written)

```typescript
// src/modules/alerts/__tests__/stock-monitor.test.ts
describe('StockMonitorService', () => {
  describe('checkStock', () => {
    it('triggers alert when stock falls below threshold', async () => {
      const mockSku = createMockSku({ currentStock: 5 });
      const mockThreshold = createMockThreshold({ quantity: 10 });
      mockPrisma.reorderThreshold.findMany.mockResolvedValue([
        { ...mockThreshold, sku: mockSku }
      ]);

      await stockMonitorService.checkStock();

      expect(mockAlertService.createAlert).toHaveBeenCalledWith({
        skuId: mockSku.id,
        warehouseId: mockThreshold.warehouseId,
        currentStock: 5,
        threshold: 10,
      });
    });

    it('does not trigger alert when stock is at threshold', async () => {
      const mockSku = createMockSku({ currentStock: 10 });
      const mockThreshold = createMockThreshold({ quantity: 10 });
      // ...
      expect(mockAlertService.createAlert).not.toHaveBeenCalled();
    });

    it('does not duplicate alert if one is already active', async () => { /* ... */ });
  });
});
```

### 9.3 E2E Test — Agent Writes, Human Runs

```bash
claude

> Write a Playwright E2E test for the Alert Management Dashboard.
> Cover:
> 1. User logs in and sees the alerts page
> 2. Filters by warehouse → table updates
> 3. Clicks "Mark as Ordered" → alert disappears from Active tab
> 4. Exports CSV → file downloads with correct headers
>
> Use our existing Playwright setup in frontend-repo/e2e/
```

### 9.4 Human QA Checklist (Staging)

Run these manually after deploying to staging:

- [ ] Push notification received on test device within 3 seconds of stock drop
- [ ] Threshold edit reflects immediately in the mobile list
- [ ] Alert badge on home screen shows correct count
- [ ] Dashboard data table filters work (warehouse, date range, status)
- [ ] Dismissing an alert removes it from the Active filter
- [ ] Notifications still work with app in background (iOS + Android)
- [ ] Screen reader (VoiceOver + TalkBack) can navigate all screens
- [ ] Offline mode: app shows cached thresholds, queues updates

---

## 10. Monitoring Setup

**Time investment:** Agent sets up, engineer verifies in Datadog.

### 10.1 Agent Configures Datadog

```bash
claude

> Set up Datadog monitoring for the Smart Reorder Alerts feature.
> 
> Create:
> 1. APM service map entry for the new stock-monitor job
> 2. Log-based metric: count of reorder.alert.triggered per
>    warehouse_id tag (for the KPI dashboard)
> 3. Monitors:
>    a. Alert: stock-monitor job hasn't run in >10 min (heartbeat)
>    b. Alert: FCM delivery failure rate >2% over 5 min
>    c. Alert: /api/v1/thresholds error rate >1% over 5 min
>    d. Warning: /api/v1/thresholds p99 latency >300ms
> 4. Dashboard panel: add to "Inventory Team" dashboard
>    - Active alerts over time (line chart)
>    - Alert dismissal rate (gauge)
>    - FCM delivery rate (gauge)
>    - Reorder completion rate (gauge)
>
> Use Datadog API key from env: DD_API_KEY
```

### 10.2 Verify Monitoring Works Before Merging

```bash
# Manually trigger a low-stock condition in staging:
curl -X POST https://staging-api.yourapp.com/api/v1/internal/trigger-stock-check \
  -H "Authorization: Bearer $INTERNAL_TOKEN" \
  -d '{"skuId": "test-sku-1", "simulatedStock": 2}'

# Then check:
# 1. Datadog logs: search for trace_id from this request
# 2. Dashboard: "Active alerts" count increments
# 3. Test device: push notification arrives
# 4. Heartbeat monitor: shows green in Datadog
```

> ✅ **Gate:** Do not merge to main until all 4 Datadog monitors show green in staging.

### 10.3 Structured Logging Pattern

Every business event the agent logs follows this format:

```typescript
// Every log entry looks like this — verify in Datadog log explorer
{
  "timestamp": "2025-01-15T10:23:45.123Z",
  "level": "info",
  "event": "reorder.alert.triggered",
  "trace_id": "abc123def456",
  "sku_id": "inv_sku_789",          // ✅ not PII
  "warehouse_id": "wh_012",         // ✅ not PII
  "current_stock": 5,
  "threshold": 10,
  "alert_id": "alert_345",
  "service": "stock-monitor",
  "env": "production"
}
```

---

## 11. Blue-Green Deployment

**Time investment:** 30 minutes, mostly automated.

### 11.1 How Blue-Green Works in This Stack

```
                    ┌─────────────────┐
    Internet ──────▶│  AWS ALB        │
                    └────────┬────────┘
                             │
              ┌──────────────▼──────────────┐
              │                             │
    ┌─────────▼────────┐      ┌─────────────▼──────┐
    │  BLUE (current)  │      │  GREEN (new version) │
    │  ECS Service     │      │  ECS Service         │
    │  v1.4.2          │      │  v1.5.0 ← deploy here│
    │  100% traffic    │      │  0% traffic initially │
    └──────────────────┘      └────────────────────────┘
              │                             │
              └──────────────┬──────────────┘
                             │
                    ┌────────▼────────┐
                    │   RDS PostgreSQL │
                    │  (shared DB)    │
                    └─────────────────┘
```

### 11.2 Pre-Deployment Agent Checklist

```bash
claude

> We're about to do a blue-green deployment of Smart Reorder Alerts.
> Run our pre-deployment checklist:
>
> 1. Confirm all INV-43 to INV-49 stories are merged to main
> 2. Confirm CI is green on main (no failing tests)
> 3. Check if there are any pending Prisma migrations
>    — if yes, remind me they run BEFORE traffic switch
> 4. List any feature flags we need to enable post-deploy
> 5. Check Datadog: are there any active incidents right now?
> 6. Summarize the rollback procedure if something goes wrong
```

### 11.3 Deployment Steps

```bash
# Step 1 — Run database migrations FIRST (backward-compatible)
# Migrations must be additive only — never drop columns in same deploy
aws ecs run-task \
  --cluster inventoryos-prod \
  --task-definition inventoryos-migrate \
  --overrides '{"containerOverrides":[{"name":"migrate","command":["npx","prisma","migrate","deploy"]}]}'

# Wait for migration task to exit 0
aws ecs wait tasks-stopped --cluster inventoryos-prod --tasks $TASK_ARN

# Step 2 — Deploy new version to GREEN (no traffic yet)
aws ecs update-service \
  --cluster inventoryos-prod \
  --service inventoryos-backend-green \
  --task-definition inventoryos-backend:$NEW_VERSION \
  --desired-count 3

# Wait for green to be stable
aws ecs wait services-stable \
  --cluster inventoryos-prod \
  --services inventoryos-backend-green

# Step 3 — Smoke test GREEN directly (before any traffic)
curl https://green.internal.yourapp.com/health
# Expected: { "status": "ok", "version": "1.5.0" }

# Step 4 — Shift 10% traffic to GREEN
aws elbv2 modify-listener-rule \
  --rule-arn $RULE_ARN \
  --actions Type=forward,ForwardConfig='{
    "TargetGroups": [
      {"TargetGroupArn": "'$BLUE_TG'", "Weight": 90},
      {"TargetGroupArn": "'$GREEN_TG'", "Weight": 10}
    ]
  }'

# Watch Datadog for 5 minutes:
# - Error rate on green must stay <0.5%
# - Latency p99 must stay <300ms

# Step 5 — Shift 50% traffic
# (repeat weight adjustment: 50/50)

# Step 6 — Full cutover: 100% to GREEN
# (weight: 0/100)

# Step 7 — Verify, then scale down BLUE
aws ecs update-service \
  --cluster inventoryos-prod \
  --service inventoryos-backend-blue \
  --desired-count 0
```

### 11.4 Automated Rollback

```bash
# If anything goes wrong, rollback in <60 seconds:
aws elbv2 modify-listener-rule \
  --rule-arn $RULE_ARN \
  --actions Type=forward,ForwardConfig='{
    "TargetGroups": [
      {"TargetGroupArn": "'$BLUE_TG'", "Weight": 100},
      {"TargetGroupArn": "'$GREEN_TG'", "Weight": 0}
    ]
  }'

# BLUE is still running — zero downtime rollback
```

### 11.5 GitHub Actions CI/CD Pipeline

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm run lint
      - run: npm run test -- --coverage
      - run: npx prisma validate

  deploy:
    needs: test
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Build and push Docker image
        run: |
          docker build -t $ECR_REPO:$GITHUB_SHA .
          docker push $ECR_REPO:$GITHUB_SHA

      - name: Run migrations
        run: aws ecs run-task ... # migration task

      - name: Deploy to GREEN
        run: aws ecs update-service ... # green service

      - name: Smoke test GREEN
        run: curl --fail https://green.internal.yourapp.com/health

      - name: Shift traffic (10% → 50% → 100%)
        run: ./scripts/shift-traffic.sh

      - name: Notify Slack
        run: |
          curl -X POST $SLACK_WEBHOOK \
            -d '{"text": "✅ v${{ github.sha }} deployed to production"}'
```

---

## 12. Go Live Checklist

Run this with the agent 30 minutes before go-live:

```bash
claude

> We're going live with Smart Reorder Alerts in 30 minutes.
> Walk me through the go-live checklist and confirm each item.
```

### Pre-Go-Live (T-30 min)

- [ ] All stories INV-43 to INV-49 in "Done" in Jira
- [ ] QA sign-off on all stories (QA engineer has signed each ticket)
- [ ] PM UAT sign-off received
- [ ] Datadog monitors all green in production (after migration)
- [ ] Rollback procedure reviewed by on-call engineer
- [ ] On-call engineer aware this is happening
- [ ] Customer support notified (if customer-facing)
- [ ] Release notes written in Notion

### Go-Live (T-0)

- [ ] Execute blue-green deployment (follow Section 11)
- [ ] Smoke test on production: create a threshold, trigger a low-stock event, receive push notification
- [ ] Verify Datadog "Active Alerts" dashboard shows data
- [ ] Verify FCM delivery monitor shows green

### Post-Go-Live (T+30 min)

- [ ] Error rate: <0.5% (check Datadog)
- [ ] Latency p99: <300ms (check Datadog)
- [ ] First real alerts triggered (check `reorder.alert.triggered` metric)
- [ ] No reports from support channel
- [ ] Scale down BLUE ECS service to 0

### Post-Go-Live (T+24 hours)

```bash
claude

> It's been 24 hours since Smart Reorder Alerts launched.
> Pull these metrics from Datadog and summarize:
> 1. Total alerts triggered
> 2. Alert dismissal rate
> 3. FCM delivery rate
> 4. Any error spikes?
> 5. p99 latency trend
>
> Compare against our KPI targets from the Notion KPI page
> and flag any targets we're at risk of missing.
```

### Jira Cleanup

```bash
claude

> Close out the Smart Reorder Alerts launch.
> 1. Move all INV-43 to INV-49 stories to "Done"
> 2. Update Jira Epic INV-42 status to "Done"
> 3. Update Notion roadmap entry to "Shipped"
> 4. Add actual ship date and link to Datadog dashboard
>    on the Notion KPI page
```

---

## 13. Cost Summary

Estimated AI agent cost for this feature end-to-end (rough order of magnitude):

| Phase | Agent Calls | Est. Tokens | Est. Cost |
|-------|------------|-------------|-----------|
| Ideation & KPI research | 3–4 sessions | ~50K | ~$0.75 |
| Notion roadmap creation | 1 session | ~10K | ~$0.15 |
| UX design brief | 1 session | ~20K | ~$0.30 |
| Jira story creation (8 stories) | 1 session | ~40K | ~$0.60 |
| Backend implementation (5 stories) | 5 sessions | ~300K | ~$4.50 |
| Frontend implementation (1 story) | 1 session | ~60K | ~$0.90 |
| Mobile implementation (2 stories) | 2 sessions | ~120K | ~$1.80 |
| Test writing | ~included above | — | — |
| Monitoring setup | 1 session | ~20K | ~$0.30 |
| PR creation + Jira updates | ~included above | — | — |
| **Total** | | **~620K tokens** | **~$9.30** |

> 💡 **Cost optimization tips:**
> - Keep sessions long rather than restarting frequently (each restart re-loads CLAUDE.md)
> - Put shared context in CLAUDE.md so you don't re-explain per session
> - Use one session per story end-to-end (branch → implement → PR)
> - The biggest cost driver is large codebases — keep CLAUDE.md precise so the agent doesn't need to explore

**Human time saved vs. fully manual:**

| Task | Manual | With Agent | Saved |
|------|--------|------------|-------|
| Story creation (8 stories) | ~4 hours | ~20 min review | ~3.5 hrs |
| Boilerplate code + tests | ~2 days | ~2 hours review | ~14 hrs |
| PR descriptions | ~2 hrs total | ~0 | ~2 hrs |
| Monitoring setup | ~3 hours | ~30 min verify | ~2.5 hrs |
| Jira/Notion updates | ~1 hour | ~0 | ~1 hr |
| **Total** | | | **~23 hours saved** |

---

*Example Playbook — AI Agent Zero to Production · v1.0 · 2026*
