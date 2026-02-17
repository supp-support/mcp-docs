# Supp MCP Server

**Manage your AI-powered support desk from any MCP-compatible AI agent.**

Supp's MCP server gives AI agents like Claude Code, Cursor, and Windsurf direct access to your support operations — classify messages, create routing rules, manage tickets, connect integrations, check billing, and more.

**43 tools** across 12 categories. Most are free to call.

## Quick Setup

### Claude Code / Cursor / Windsurf

Add to your MCP settings:

```json
{
  "mcpServers": {
    "supp": {
      "url": "https://mcp.supp.support/mcp",
      "transport": "streamable-http"
    }
  }
}
```

On first use, you'll be prompted to authorize via OAuth. That's it.

### API Key Authentication

For non-interactive or server-side setups:

```json
{
  "mcpServers": {
    "supp": {
      "url": "https://mcp.supp.support/mcp",
      "transport": "streamable-http",
      "headers": {
        "Authorization": "Bearer sk_live_your_secret_key"
      }
    }
  }
}
```

## What Can You Do?

Talk to your AI agent naturally:

- *"Classify this message and show me the intent"*
- *"Create a routing rule for bug reports that creates GitHub issues"*
- *"Show me all escalated tickets from the last week"*
- *"Connect my Slack workspace"*
- *"What's my current balance?"*
- *"Auto-escalate all critical priority messages"*
- *"Route all billing intents to our Slack #billing channel"*
- *"Export last month's data as CSV"*

## Documentation

| Section | Description |
|---------|-------------|
| [Tools Reference](./tools-reference.md) | Complete reference for all 43 tools with parameters and examples |
| [Workflows](./workflows.md) | Common workflows and step-by-step guides |
| [Classification](./classification.md) | How intent classification works (315 intents, confidence, priority) |
| [Routing](./routing.md) | Setting up routing rules, pipelines, and bulk configuration |
| [Integrations](./integrations.md) | Connecting GitHub, Slack, Linear, Jira, and 12 more |
| [Billing](./billing.md) | Pricing, credits, spend caps, and refunds |

## Tool Summary

| Category | Tools | Description |
|----------|-------|-------------|
| [Classification](#classification) | 4 | Classify messages, list intents, list categories |
| [Tickets](#tickets) | 3 | List, view, and resolve support tickets |
| [Triggers](#triggers) | 7 | Create, update, delete, simulate, and execute routing rules |
| [Routing](#routing) | 3 | Bulk configure intents by category or list |
| [Priority](#priority) | 6 | Score urgency, manage blanket rules, per-intent overrides |
| [Integrations](#integrations) | 3 | Connect, list, and disconnect external services |
| [Escalation](#escalation) | 2 | List and respond to escalated tickets |
| [Approvals](#approvals) | 3 | Review, approve, and reject pending actions |
| [Analytics](#analytics) | 4 | Metrics, intent breakdown, trigger performance, exports |
| [Billing](#billing) | 4 | Balance, spend caps, refunds |
| [API Keys](#api-keys) | 1 | Generate new API key pairs |
| [Knowledge](#knowledge) | 2 | Connect documentation sources |
| [Help](#help) | 1 | On-demand documentation |

**Total: 43 tools** | **Free: 39** | **Paid: 4** ($0.03 - $0.23 per call)

## Pricing

Most tools are free. Only classification and priority scoring incur charges:

| Tool | Cost |
|------|------|
| `supp_classify` | $0.20 |
| `supp_classify_with_priority` | $0.23 |
| `supp_priority_score` | $0.03 |
| `supp_trigger_execute` | Varies (integration action cost) |
| Everything else | Free |

New accounts get **$5.00 in free credits** on signup.

## Links

- **Website**: [supp.support](https://supp.support)
- **Dashboard**: [supp.support/dashboard](https://supp.support/dashboard)
- **Full Documentation**: [docs](../docs/)
