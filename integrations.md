# Integrations

Connect external services so Supp can automatically create issues, post messages, send emails, and trigger webhooks when tickets are classified.

## Supported Services

### Issue Tracking

| Provider | Connection | Action |
|----------|------------|--------|
| **GitHub** | OAuth or token | Create issues with labels and priority tags |
| **Linear** | OAuth or API key | Create issues with priority, team, and project |
| **Jira** | OAuth or basic auth | Create issues with project and type |
| **Notion** | OAuth | Create pages in databases |

### Communication

| Provider | Connection | Action |
|----------|------------|--------|
| **Slack** | OAuth or webhook | Post to channels with rich Block Kit formatting |
| **Discord** | Webhook | Post to channels |
| **Microsoft Teams** | Webhook | Post to channels |
| **Email (Resend)** | API key | Send templated emails |

### CRM & Support

| Provider | Connection | Action |
|----------|------------|--------|
| **Zendesk** | API key | Create support tickets |
| **Intercom** | OAuth or API key | Create conversations |
| **HubSpot** | API key | Create tickets in pipelines |
| **Shopify** | API key | Tag orders, update customers |

### Automation

| Provider | Connection | Action |
|----------|------------|--------|
| **Zapier** | Webhook URL | Trigger any Zap workflow |
| **Custom** | HTTPS URL | POST JSON to any endpoint |

## Priority Mapping

When creating issues, Supp maps priority levels automatically:

| Supp Priority | GitHub Label | Linear | Jira |
|---------------|-------------|--------|------|
| critical | P0 | Urgent | Highest |
| high | P1 | High | High |
| medium | P2 | Medium | Medium |
| low | P3 | Low | Low |

## MCP Tools

| Tool | Description |
|------|-------------|
| `supp_integrations` | List connected and available services |
| `supp_connect` | Get OAuth URL or setup instructions |
| `supp_disconnect` | Disconnect a service |

## Connecting via MCP

```
You: "What integrations are available?"
→ supp_integrations()

You: "Connect GitHub"
→ supp_connect(service: "github")
   Returns: OAuth URL — open to authorize

You: "Connect Slack"
→ supp_connect(service: "slack")
   Returns: OAuth URL

You: "Disconnect Linear"
→ supp_disconnect(service: "linear")
```

## Multiple Connections

You can connect multiple instances of the same provider:
- Two Slack workspaces (engineering + support)
- Multiple GitHub repos
- Different email addresses for different intents

Specify which integration to use in each routing rule's config.

## Token Management

Supp automatically detects expired tokens and prompts you to reconnect. If a token fails 3 consecutive verification checks, the integration is auto-disconnected.
