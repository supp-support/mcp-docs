# Routing

Routing rules determine what happens when a message is classified. Map intents to actions — template responses, integration calls, escalations, or multi-step pipelines.

## Action Types

| Type | Description |
|------|-------------|
| `mcp_action` | Call an integration (GitHub issue, Slack message, etc.) |
| `template` | Send a pre-written response with variable interpolation |
| `escalate` | Route to a human agent |
| `none` | Classify only, no action |

## Template Variables

Use these in response templates:

| Variable | Value |
|----------|-------|
| `{{intent}}` | Classified intent |
| `{{confidence}}` | Confidence percentage |
| `{{message}}` | Customer's message |
| `{{channel}}` | Channel type |
| `{{priority}}` | Priority level |
| `{{app_url}}` | Your app URL |
| `{{fields.key}}` | Collected field values |

## Multi-Action Pipelines

Chain actions together:

```json
[
  {"type": "mcp_action", "provider": "github", "config": {"labels": "bug"}},
  {"type": "mcp_action", "provider": "slack", "config": {"channel": "#alerts"}},
  {"type": "template", "template": "We've filed a ticket and notified the team."}
]
```

`escalate_human` is terminal — nothing can follow it.

## Priority Filtering

Only trigger rules for specific priority levels:

```json
{"priority_filter": ["high", "critical"]}
```

If no filter is set, the rule fires for any priority.

## Collect Fields

Collect information from the customer before executing:

```json
[
  {"key": "email", "label": "Your email", "type": "email", "required": true},
  {"key": "description", "label": "Details", "type": "text", "required": false}
]
```

Supported types: `email`, `number`, `text`, `tel`

## MCP Tools

| Tool | Description |
|------|-------------|
| `supp_triggers` | List your custom triggers |
| `supp_trigger_get` | Get single trigger details |
| `supp_trigger_create` | Create a new routing rule |
| `supp_trigger_update` | Update a rule |
| `supp_trigger_delete` | Delete a rule |
| `supp_trigger_simulate` | Dry-run (no side effects) |
| `supp_trigger_execute` | Execute actions for real |
| `supp_routing_list` | View full routing table |
| `supp_routing_bulk` | Bulk update multiple intents |
| `supp_routing_category` | Configure entire categories |

## Bulk Configuration

### By Intent List

```
supp_routing_bulk(
  intents: ["bug_report", "crash_report", "performance_issue"],
  action: "mcp_action",
  config: {provider: "github", labels: "bug"}
)
```

### By Category

```
supp_routing_category(
  categories: ["billing_payment"],
  action: "mcp_action",
  config: {provider: "slack", channel: "#billing"}
)
```

### All Intents

```
supp_routing_category(
  categories: ["all"],
  action: "template",
  config: {response_template: "Thanks for reaching out. We'll get back to you shortly."}
)
```

Up to 500 intents per batch request.
