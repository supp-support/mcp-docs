# Billing

Supp uses **pay-per-resolution** pricing. Purchase prepaid credits, get charged only for successful actions.

## Pricing

| Action | Cost |
|--------|------|
| Classification + template response | $0.20 |
| Classification + integration action | $0.30 |
| Priority scoring | +$0.03 |
| Batch classification | 50% discount |

**MCP tool costs:**

| Tool | Cost |
|------|------|
| `supp_classify` | $0.20 |
| `supp_classify_with_priority` | $0.23 |
| `supp_priority_score` | $0.03 |
| `supp_trigger_execute` | Varies |
| Everything else | Free |

## Free Credits

New accounts get **$5.00 in free credits** — enough for ~25 classifications.

## Spend Caps

Every API key has a spend limit (default: $5.00/day) to prevent unexpected charges.

```
supp_spend_cap(action: "view", key_id: "key_abc")
→ $5.00/day cap, $3.20 used, $1.80 remaining

supp_spend_cap(action: "update", key_id: "key_abc", spend_cap: 10.00)
→ Cap updated to $10.00/day

supp_spend_cap(action: "approve_overage", key_id: "key_abc", duration_hours: 48)
→ Overage approved for 48 hours
```

## MCP Tools

| Tool | Description |
|------|-------------|
| `supp_balance` | Check balance and recent charges |
| `supp_spend_cap` | View/update spend caps |
| `supp_refund` | Submit a refund request |
| `supp_refund_status` | Check refund request status |

## Refunds

If an integration action fails after charging, credits are automatically refunded.

For other refund requests:

```
supp_refund(
  reason: "Unexpected charges from compromised API key",
  amount: 15.00,
  contact_method: "email"
)
→ Returns refund_id for tracking

supp_refund_status()
→ Shows status of all refund requests
```

Refund requests are reviewed within 1-2 business days.

## Monitoring

```
supp_balance()
→ Current balance + recent charges

supp_stats(period: "month")
→ Total tickets, resolution rate, total cost

supp_stats_export(period: "month", format: "csv")
→ Download URL for detailed export
```
