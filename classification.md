# Classification

Supp classifies customer messages into **315 intents** across **13 categories** using a custom-trained ML model.

## How It Works

1. Customer sends a message
2. The model returns the most likely intent + confidence score
3. If confidence exceeds the threshold, the routing rule fires
4. If confidence is too low, the message is escalated to a human

Classification latency is ~100-200ms.

## Categories

| Category | Intents | Examples |
|----------|---------|---------|
| **No Intent** | 8 | Off-topic, self-talk, humor, rambling |
| **Account Management** | 31 | Password reset, 2FA setup, account deletion |
| **Billing & Payment** | 31 | Refund request, invoice dispute, payment failed |
| **Subscription Management** | 21 | Upgrade, downgrade, pause, cancel |
| **Product Inquiry** | 31 | Availability, pricing, feature comparison |
| **Technical Support** | 47 | Bug report, performance issue, API troubleshooting |
| **Order & Delivery** | 23 | Order tracking, shipping delay, cancellation |
| **Returns & Refunds** | 24 | Return request, exchange, defective product |
| **Feature Requests** | 22 | New feature, UI improvement, integration request |
| **Complaints & Feedback** | 20 | Service complaint, negative experience |
| **General Inquiry** | 15 | Business hours, contact info |
| **Verification & Auth** | 19 | Identity verification, biometric setup |
| **Loyalty & Rewards** | 14 | Points balance, referral codes, cashback |

Use `supp_categories` to list all categories, and `supp_intents` to browse intents within a category.

## Confidence Thresholds

| Threshold | Default | What Happens |
|-----------|---------|-------------|
| Escalation floor | 0.35 | Below this → always escalate to human |
| Auto-success | 0.70 | Above this → charge immediately |
| Routing rule | 0.80 | Per-rule minimum to trigger the action |

You can customize the routing rule threshold per intent.

## MCP Tools

| Tool | Cost | Description |
|------|------|-------------|
| `supp_classify` | $0.20 | Classify a message |
| `supp_classify_with_priority` | $0.23 | Classify + score priority |
| `supp_intents` | Free | List all 315 intents |
| `supp_categories` | Free | List the 13 categories |

## Priority Scoring

Priority scoring runs alongside classification and returns a urgency level:

| Level | Description |
|-------|-------------|
| `low` | Routine questions |
| `medium` | Standard support requests |
| `high` | Urgent issues |
| `critical` | Outages, security incidents, data loss |

Use `supp_priority_score` ($0.03) to score a message, or pass `include_priority: true` with classification.

## Batch Classification

For bulk operations through the API, batch mode provides a **50% discount** per classification.

## Classification Feedback

When customers flag an incorrect classification via the widget, that data is collected and used to improve the model over time. Enable/disable in your dashboard settings.
