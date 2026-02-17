# Workflows

Common workflows for managing Supp through an AI agent.

## Getting Started

### First-Time Setup

```
You: "Help me get started with Supp"

→ supp_help(topic: "setup")
   Returns step-by-step setup guide

You: "Create an API key"

→ supp_api_key_create(label: "Production")
   Returns:
     pk_live_abc123... (for your website widget)
     sk_live_xyz789... (keep private, for server-side)

You: "Connect my GitHub account"

→ supp_connect(service: "github")
   Returns: OAuth URL — open it to authorize
```

### Explore Available Intents

```
You: "What intents can Supp classify?"

→ supp_categories()
   Returns: 13 categories with counts

You: "Show me technical support intents"

→ supp_intents(category: "technical_support")
   Returns: 47 intents (bug_report, performance_issue, api_troubleshooting, ...)
```

---

## Setting Up Routing

### Single Intent Rule

```
You: "Route bug reports to GitHub"

→ supp_integrations()
   Finds: GitHub connected (org/app)

→ supp_trigger_create(
    name: "Bug Reports → GitHub",
    when: "bug_report",
    action: "mcp_action",
    config: {provider: "github", repo: "org/app", labels: "bug"}
  )

→ supp_trigger_simulate(
    trigger_id: "trg_abc",
    sample_text: "The app crashes when I open settings"
  )
   Returns: would_fire: true, intent: bug_report, confidence: 0.91
```

### Multi-Action Pipeline

```
You: "When someone reports a critical bug, create a GitHub issue AND alert Slack"

→ supp_trigger_create(
    name: "Critical Bugs → GitHub + Slack",
    when: "bug_report",
    action: "mcp_action",
    priority_filter: ["critical"],
    actions: [
      {type: "mcp_action", provider: "github", config: {labels: "bug,critical"}},
      {type: "mcp_action", provider: "slack", config: {channel: "#incidents"}},
      {type: "template", template: "We've filed a ticket and alerted our engineering team."}
    ]
  )
```

### Template Response

```
You: "Send a canned response for password reset requests"

→ supp_trigger_create(
    name: "Password Reset → Template",
    when: "password_reset",
    action: "template",
    config: {response_template: "To reset your password, visit {{app_url}}/reset. If you need further help, reply here."}
  )
```

### Bulk Configuration

```
You: "Route all billing intents to Slack #billing"

→ supp_routing_category(
    categories: ["billing_payment"],
    action: "mcp_action",
    config: {provider: "slack", channel: "#billing"}
  )
   Returns: 31 intents configured

You: "Escalate all complaints to human agents"

→ supp_routing_category(
    categories: ["complaints_feedback"],
    action: "escalate"
  )
   Returns: 20 intents configured
```

---

## Managing Tickets

### Daily Review

```
You: "How are things looking this week?"

→ supp_stats(period: "week")
   Returns: 142 tickets, 83% auto-resolved, avg confidence 0.82

You: "What are the most common intents?"

→ supp_stats_intents(period: "week", limit: 5)
   Returns: bug_report (32), password_reset (28), refund_request (19), ...

You: "Any escalated tickets?"

→ supp_escalated(limit: 10)
   Returns: 3 escalated tickets
```

### Respond to Escalations

```
You: "Show me that escalated login issue"

→ supp_ticket(ticket_id: "conv_abc")
   Returns: Full ticket with messages

You: "Draft a response explaining how to reset their 2FA"

→ supp_escalation_respond(
    ticket_id: "conv_abc",
    message: "Hi! To reset your 2FA, go to Settings > Security > Two-Factor Authentication and click 'Reset'. You'll receive a verification code at your email.",
    resolve: true
  )
```

### Handle Approvals

```
You: "Are there any pending approvals?"

→ supp_queue()
   Returns: 2 pending approvals

You: "Approve the first one, reject the second with a note"

→ supp_approve(approval_id: "apr_abc")
→ supp_reject(approval_id: "apr_xyz", reason: "Customer already resolved this via email")
```

---

## Priority Scoring

### Enable and Configure

```
You: "Enable priority scoring"

→ supp_priority_scoring_settings(enabled: true)

You: "Auto-escalate all critical messages"

→ supp_priority_rule_set(
    priority_level: "critical",
    action: "escalate",
    enabled: true
  )

You: "Post high-priority messages to Slack"

→ supp_priority_rule_set(
    priority_level: "high",
    action: "mcp_action",
    config: {provider: "slack", channel: "#urgent"}
  )
```

### Per-Intent Override

```
You: "Disable priority scoring for general inquiries but keep it for billing"

→ supp_priority_scoring_intent(
    intents: ["general_question", "business_hours", "contact_information"],
    enabled: false
  )
```

### Test Priority Scoring

```
You: "What priority would this get: 'Our entire team is locked out'"

→ supp_priority_score(text: "Our entire team is locked out")
   Returns: priority: "critical", cost: $0.03
```

---

## Integrations

### Connect Services

```
You: "Connect Slack"

→ supp_connect(service: "slack")
   Returns: OAuth URL — open to authorize

You: "What services are available?"

→ supp_integrations()
   Returns: Connected: [GitHub, Slack], Available: [Linear, Jira, Notion, ...]

You: "Connect Linear"

→ supp_connect(service: "linear")
   Returns: OAuth URL
```

### Verify Connections

```
You: "Test my GitHub integration"

→ supp_trigger_execute(intent: "bug_report")
   Creates a real test issue in GitHub
   Returns: success, issue URL
```

---

## Billing & Monitoring

### Check Spending

```
You: "What's my balance?"

→ supp_balance()
   Returns: $47.50 USD, recent charges

You: "Show me spend caps"

→ supp_spend_cap(action: "view", key_id: "key_abc")
   Returns: $5.00/day, $3.20 used today, $1.80 remaining
```

### Adjust Spend Caps

```
You: "Increase the spend cap to $20/day for our launch"

→ supp_spend_cap(
    action: "approve_overage",
    key_id: "key_abc",
    duration_hours: 48
  )

You: "Set the permanent cap to $10/day"

→ supp_spend_cap(
    action: "update",
    key_id: "key_abc",
    spend_cap: 10.00,
    spend_window: "daily"
  )
```

### Export Data

```
You: "Export last month's data as CSV"

→ supp_stats_export(period: "month", format: "csv")
   Returns: download URL (expires in 24 hours)
```

---

## Testing & Debugging

### Test Classification

```
You: "How would Supp classify 'I need a refund for my last order'?"

→ supp_classify(text: "I need a refund for my last order")
   Returns: intent: "refund_initiation", confidence: 0.94
```

### Simulate a Trigger

```
You: "Would the refund trigger fire for 'I want my money back'?"

→ supp_trigger_simulate(
    trigger_id: "trg_refund",
    sample_text: "I want my money back"
  )
   Returns: would_fire: true, confidence: 0.87
```

### Debug a Trigger

```
You: "Show me the refund trigger config"

→ supp_trigger_get(trigger_id: "trg_refund")
   Returns: Full config with pipeline, template, threshold, etc.

You: "What's the trigger performance?"

→ supp_stats_triggers(trigger_id: "trg_refund", period: "month")
   Returns: fired 312 times, 298 auto-resolved, 14 rejected
```
