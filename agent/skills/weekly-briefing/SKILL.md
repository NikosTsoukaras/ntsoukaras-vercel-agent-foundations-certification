---
name: weekly-briefing
description: >-
  Produce a weekly back-office briefing for the Vercel Swag Store, covering top
  sellers, low-stock alerts, open returns, open support tickets, and recommended
  actions for the week ahead. Use whenever the user asks for a weekly briefing,
  weekly report, weekly recap, store status summary, back-office overview,
  "how did the store do this week", or anything resembling a Monday-morning
  operational rundown or recap, even if they don't use the exact word "briefing".
---

# Weekly back-office briefing

You are producing an operational weekly report for a store owner. The point is
to give them everything they need to plan the week ahead, in one page, without
having to dig through dashboards.

## Step 1: Gather data in parallel

These four calls are independent. Issue them in a single turn so they fan out;
do not wait for one result before issuing the others:

- `list_stock_level_for_products` with `lowStock: "true"`
- `sales_totals_by_product` with `from` set to 7 days before today
- `list_historical_returns` with `from` set to 7 days before today
- `list_support_tickets` with `status: "open"` and `from` set to 7 days before today

Compute `from` as `YYYY-MM-DD` seven days before today, using the current date
provided by the runtime. If any one call fails, continue with the others and note
the gap in the corresponding section. Missing data is not a zero count. If a
response is paginated or limited, label the coverage rather than presenting a
partial count as a store-wide total.

## Step 2: Synthesize, don't just dump

- **Top sellers:** top 3-5 by units sold. Revenue is in cents. Divide by 100 and
  format as `$X.XX`.
- **Low stock:** separate `stock: 0` (out) from `lowStock: true` (running low,
  with stock greater than zero). Out-of-stock bestsellers are most urgent.
- **Returns:** count by decision; call out any product appearing in 3+ returns.
  Treat undecided or `needs_info` returns as pending, and identify any requiring
  more information.
- **Support tickets:** group by priority; flag dominant categories.
- **Recommended actions:** 3-5 concrete next steps, each tied to a specific row
  or finding above. Do not invent actions to meet the count when data is missing.

## Step 3: Render the report

Use this fixed structure every week. Show today's date as `YYYY-MM-DD`. State
explicitly when a section has no matching records or when its data is unavailable.

```markdown
# Weekly Briefing: week ending {today's date}

## Top Sellers (last 7 days)

| Product | Units | Orders | Revenue |
| ------- | ----- | ------ | ------- |
| ...     | ...   | ...    | $XX.XX  |

## Low Stock Alerts

- **Out of stock:** {product} ({id})
- **Running low:** {product}, {n} units left

## Returns This Week

{n total}: {n approved}, {n rejected}, {n pending}
{Products appearing in 3+ returns, if any, with their return counts}

## Open Support Tickets

{n total}: {n urgent}, {n high}, {n normal}, {n low}
{Dominant categories, with counts}

## Recommended Actions for the Week Ahead

1. {action tied to data above}
2. ...
```

Tie every recommendation back to a specific row or finding above. Generic
checklists defeat the point of the briefing.
