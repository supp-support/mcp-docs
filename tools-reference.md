# Tools Reference

Complete reference for all 43 Supp MCP tools.

---

## Classification

### `supp_classify`

Classify a message into one of 315 intents. **Costs $0.20 per call.**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `text` | string | Yes | Message text to classify |

```json
// Returns
{
  "intent": "bug_report",
  "confidence": 0.91,
  "all_intents": [
    {"intent": "bug_report", "confidence": 0.91},
    {"intent": "performance_issue", "confidence": 0.68}
  ]
}
```

### `supp_classify_with_priority`

Classify intent AND score priority in a single call. **Costs $0.23 per call** ($0.20 classification + $0.03 priority).

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `text` | string | Yes | Message text to classify and score |

```json
// Returns
{
  "intent": "api_troubleshooting",
  "confidence": 0.88,
  "priority": "high",
  "all_intents": [...],
  "cost": 0.23,
  "new_balance": 4.77
}
```

### `supp_intents`

List all 315 supported intents, optionally filtered by category.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `category` | enum | No | `"all"` | One of: `no_intent`, `account_management`, `billing_payment`, `subscription_management`, `product_inquiry`, `technical_support`, `order_delivery`, `returns_refunds`, `feature_requests`, `complaints_feedback`, `general_inquiry`, `verification_authentication`, `loyalty_rewards`, `all` |

```json
// Returns
{
  "total": 315,
  "categories": [
    {
      "category": "technical_support",
      "count": 47,
      "intents": [
        {"id": "bug_report", "description": "Customer reporting a software bug"},
        {"id": "performance_issue", "description": "..."}
      ]
    }
  ]
}
```

### `supp_categories`

List all 13 intent categories with counts.

No parameters.

```json
// Returns
{
  "total_intents": 315,
  "categories": [
    {"id": "technical_support", "name": "Technical Support", "intent_count": 47},
    {"id": "billing_payment", "name": "Billing & Payment", "intent_count": 31}
  ]
}
```

---

## Tickets

### `supp_tickets`

List support tickets with filters.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `status` | enum | No | `"all"` | `"open"`, `"resolved"`, `"all"` |
| `intent` | string | No | — | Filter by intent (e.g., `"bug_report"`) |
| `limit` | number | No | 50 | 1-100 |
| `since` | string | No | — | ISO date (e.g., `"2025-01-15"`) |
| `until` | string | No | — | ISO date (e.g., `"2025-01-31"`) |

```json
// Returns
{
  "tickets": [
    {
      "id": "conv_abc",
      "message": "The app keeps crashing",
      "intent": "bug_report",
      "confidence": 0.91,
      "status": "open",
      "user_email": "customer@example.com",
      "created_at": "2025-01-15T10:30:00Z"
    }
  ]
}
```

### `supp_ticket`

Get full details of a single ticket.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `ticket_id` | string | Yes | Ticket ID |

```json
// Returns
{
  "id": "conv_abc",
  "message": "The app keeps crashing",
  "intent": "bug_report",
  "confidence": 0.91,
  "status": "open",
  "user_email": "customer@example.com",
  "created_at": "2025-01-15T10:30:00Z",
  "trigger_fired": "trg_xyz",
  "action_status": "completed"
}
```

### `supp_ticket_resolve`

Mark a ticket as resolved.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `ticket_id` | string | Yes | Ticket ID to resolve |

---

## Triggers (Routing Rules)

### `supp_triggers`

List your custom triggers. Default: top 20.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `limit` | number | No | 20 | 1-100 |
| `offset` | number | No | 0 | Pagination offset |

### `supp_trigger_get`

Get a single trigger by ID.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `trigger_id` | string | Yes | Trigger ID |

Returns full trigger config including pipeline, template, approval settings, priority filter, and collect fields.

### `supp_trigger_create`

Create a new routing rule.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Human-readable name |
| `when` | string | Yes | Intent to trigger on (e.g., `"bug_report"`) |
| `action` | string | Yes | `"mcp_action"`, `"template"`, `"escalate"`, or `"none"` |
| `config` | object | No | Action config (must include `provider` for integrations) |
| `response` | object | No | Response template config |
| `approval` | boolean | No | Require human approval (default: false) |
| `priority_filter` | string[] | No | Only fire for these priority levels |
| `actions` | array | No | Multi-action pipeline (see below) |
| `collect_fields` | array | No | Fields to collect before executing |

**Pipeline actions** (for `actions` parameter):

```json
[
  {"type": "mcp_action", "provider": "github", "config": {"repo": "org/app", "labels": "bug"}},
  {"type": "mcp_action", "provider": "slack", "config": {"channel": "#alerts"}},
  {"type": "template", "template": "We've filed a ticket for your issue."}
]
```

`escalate_human` is terminal — nothing can follow it in the pipeline.

**Collect fields**:

```json
[
  {"key": "email", "label": "Your email", "type": "email", "required": true},
  {"key": "description", "label": "Describe the issue", "type": "text", "required": false}
]
```

### `supp_trigger_update`

Update an existing trigger. Only specified fields are changed.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `trigger_id` | string | Yes | Trigger ID |
| `approval` | boolean | No | Require approval |
| `template` | string | No | Response template |
| `enabled` | boolean | No | Enable/disable |
| `action` | string | No | New action type |
| `config` | object | No | Updated config |
| `priority_filter` | string[] | No | Priority filter (empty array to clear) |
| `actions` | array | No | Replace pipeline |
| `collect_fields` | array/null | No | Replace fields (null to clear) |

### `supp_trigger_delete`

Delete a trigger permanently.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `trigger_id` | string | Yes | Trigger ID |

### `supp_trigger_simulate`

Dry-run a trigger against sample text. No side effects, no charges.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `trigger_id` | string | Yes | Trigger ID |
| `sample_text` | string | Yes | Sample message to test |

```json
// Returns
{
  "would_fire": true,
  "intent_matched": "bug_report",
  "confidence": 0.89,
  "action_preview": "Create GitHub issue",
  "approval_required": false
}
```

### `supp_trigger_execute`

Execute a trigger's integration actions for real. **Creates actual issues, sends real messages.**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `trigger_id` | string | No* | Trigger ID (*one of trigger_id or intent required) |
| `intent` | string | No* | Intent name (*one of trigger_id or intent required) |

---

## Routing (Bulk Configuration)

### `supp_routing_list`

List the routing table grouped by category.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `category` | string | No | Category ID for full details (omit for summary) |
| `enabled_only` | boolean | No | Only show enabled intents (default: false) |

Without `category`: returns compact summary (count per category).
With `category`: returns full details for every intent in that category.

### `supp_routing_bulk`

Apply routing config to multiple intents at once.

**Mode 1 — Same config for all:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `intents` | string[] | Yes | Intent IDs (max 500) |
| `action` | string | Yes | Action type |
| `config` | object | No | Action config |
| `confidence_threshold` | number | No | 0-1 (default: 0.80) |
| `approval` | boolean | No | Require approval |
| `priority_filter` | string[] | No | Priority filter |
| `actions` | array | No | Multi-action pipeline |
| `collect_fields` | array | No | Fields to collect |

**Mode 2 — Per-intent overrides:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `rules` | array | Yes | `[{intent, action?, config?, actions?}]` (max 500) |
| `action` | string | Yes | Default action type |
| `config` | object | No | Default config (overridden by per-rule config) |

### `supp_routing_category`

Apply routing config to all intents in entire categories.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `categories` | string[] | Yes | Category IDs or `["all"]` |
| `action` | string | Yes | Action type |
| `config` | object | No | Action config |
| `confidence_threshold` | number | No | 0-1 (default: 0.80) |
| `approval` | boolean | No | Require approval |
| `priority_filter` | string[] | No | Priority filter |
| `actions` | array | No | Multi-action pipeline |
| `collect_fields` | array | No | Fields to collect |

---

## Priority Scoring

### `supp_priority_score`

Score a message's urgency. **Costs $0.03 per call.**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `text` | string | Yes | Message text to score |

```json
// Returns
{"priority": "critical", "cost": 0.03}
```

Priority levels: `low`, `medium`, `high`, `critical`.

### `supp_priority_rules`

List blanket priority rules (one per priority level). No parameters.

### `supp_priority_rule_set`

Create or update a blanket priority rule.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `priority_level` | enum | Yes | `"low"`, `"medium"`, `"high"`, `"critical"` |
| `action` | string | Yes | `"mcp_action"`, `"template"`, `"escalate"`, `"none"` |
| `config` | object | No | Action config |
| `enabled` | boolean | No | Active/inactive (default: true) |

### `supp_priority_rule_delete`

Delete a blanket priority rule.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `rule_id` | string | Yes | Rule ID |

### `supp_priority_scoring_settings`

Get or set global priority scoring.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `enabled` | boolean | No | Set on/off (omit to read current value) |

### `supp_priority_scoring_intent`

Override priority scoring for specific intents.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `intents` | string[] | Yes | Intent IDs (1-500) |
| `enabled` | boolean/null | Yes | `true` = force on, `false` = force off, `null` = inherit |

---

## Integrations

### `supp_integrations`

List all connected and available integrations. No parameters.

Returns `connected` (active services) and `available` (services you can connect with setup hints).

### `supp_connect`

Get the OAuth URL or setup instructions for a service.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `service` | string | Yes | Service name (e.g., `"github"`, `"slack"`, `"linear"`) |

OAuth services return a URL you can open to authorize. Non-OAuth services return instructions.

### `supp_disconnect`

Disconnect a service.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `service` | string | Yes | Service name |

---

## Escalation

### `supp_escalated`

List escalated tickets needing human attention.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `intent` | string | No | — | Filter by intent |
| `limit` | number | No | 25 | 1-100 |

### `supp_escalation_respond`

Send a response to an escalated ticket. Saved as a draft for human review.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `ticket_id` | string | Yes | — | Ticket ID |
| `message` | string | Yes | — | Response message (1-10,000 chars) |
| `resolve` | boolean | No | false | Resolve the ticket after responding |

---

## Approvals

### `supp_queue`

List pending approval requests. No parameters.

### `supp_approve`

Approve a pending action. Executes the configured action immediately.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `approval_id` | string | Yes | Approval ID |

### `supp_reject`

Reject a pending action. Ticket stays open.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `approval_id` | string | Yes | Approval ID |
| `reason` | string | No | Reason for rejection |

---

## Analytics

### `supp_stats`

Overview metrics for a time period.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `period` | enum | No | `"week"` | `"today"`, `"week"`, `"month"`, `"all"` |

```json
// Returns
{
  "period": "week",
  "total_tickets": 142,
  "resolved": 118,
  "escalated": 24,
  "auto_resolution_rate": 0.83,
  "avg_confidence": 0.82
}
```

### `supp_stats_intents`

Intent frequency breakdown.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `period` | enum | No | `"week"` | Time period |
| `limit` | number | No | 10 | 1-50 |

### `supp_stats_triggers`

Trigger performance metrics (fires, resolutions, rejections).

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `period` | enum | No | `"week"` | Time period |
| `trigger_id` | string | No | — | Single trigger (omit for all) |
| `limit` | number | No | 20 | 1-100 |
| `offset` | number | No | 0 | Pagination |

### `supp_stats_export`

Export data as CSV or JSON.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `period` | enum | No | `"month"` | Time period |
| `format` | enum | No | `"json"` | `"csv"` or `"json"` |

---

## Billing

### `supp_balance`

Check current credit balance and recent charges. No parameters.

### `supp_spend_cap`

View or update spend caps for an API key.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `action` | enum | Yes | `"view"`, `"update"`, `"approve_overage"` |
| `key_id` | string | Yes | API key ID |
| `spend_cap` | number | For update | New cap ($0.10-$10,000) |
| `spend_window` | enum | For update | `"daily"` or `"weekly"` |
| `duration_hours` | number | For overage | Hours to allow overage (1-168) |

### `supp_refund`

Submit a refund request.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `reason` | string | Yes | Description of the issue (10-2,000 chars) |
| `amount` | number | No | Specific amount ($0.01-$10,000) |
| `transaction_ids` | string[] | No | Transaction IDs to flag (max 50) |
| `contact_method` | enum | No | `"email"`, `"widget"`, `"form"` |
| `contact_detail` | string | No | Contact info (max 200 chars) |

### `supp_refund_status`

Check status of refund requests. No parameters.

---

## API Keys

### `supp_api_key_create`

Generate a new API key pair. **Keys are shown once and cannot be retrieved again.**

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `label` | string | No | `"Default"` | Label for the key pair (max 100 chars) |

```json
// Returns
{
  "publishable_key": "pk_live_abc123...",
  "secret_key": "sk_live_xyz789...",
  "label": "Production",
  "warning": "Save these keys now — they cannot be retrieved again."
}
```

- `pk_live_` key: safe for client-side (widget embedding)
- `sk_live_` key: server-side only (keep private)

---

## Knowledge

### `supp_docs_connect`

Link a documentation source.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `source` | enum | Yes | `"notion"`, `"github"`, `"url"` |
| `url` | string | Yes | URL of the documentation source |

### `supp_docs`

List connected documentation sources and their sync status. No parameters.

---

## Help

### `supp_help`

On-demand documentation about Supp.

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `topic` | enum | No | `"overview"` | `"overview"`, `"installation"`, `"tools"`, `"setup"`, `"triggers"`, `"classification"`, `"approvals"`, `"integrations"`, `"stats"`, `"workflows"`, `"all"` |
