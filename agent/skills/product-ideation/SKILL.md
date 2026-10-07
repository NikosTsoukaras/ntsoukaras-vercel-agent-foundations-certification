---
name: product-ideation
description: >-
  Generate ranked, auditable product ideas for the Vercel Swag Store by cross-referencing recent Vercel news, current sales performance, return patterns, and the existing catalog. Use whenever the user asks for new product ideas, what to add to the catalog, swag suggestions, design inspiration, "what should we make next", product brainstorm, or anything about expanding the store.
---

# Product ideation

Produce **auditable** product recommendations. Every idea should be traceable to specific data the owner can verify, not vibes.

## Step 1: Gather inputs in parallel

Issue these four calls in a single turn:

- `researcher` subagent with `message`: "Investigate Vercel announcements, product launches, and developer-facing news from roughly the last 60 days. Be sure to compute the current date and don't rely on your memory. The Vercel Swag Store sells t-shirts, hoodies, mugs, hats, stationery, and tech accessories. Identify Vercel themes, product names, or moments that could inspire new swag designs. Return a short brief with the most promotable themes and concrete naming hooks, with sources."
- `sales_totals_by_product` with `from` set to 30 days before today
- `list_historical_returns` with `from` set to 30 days before today
- `list_products` with no filters

Compute both dates as `YYYY-MM-DD` using the current date provided by the runtime. If a call fails or returns partial/paginated data, state that coverage gap; missing data is not zero.

## Step 2: Apply noise floors

Small numbers lie:

- **Returns are a pattern only at 3+** returns for the same product.
- **Sales rankings need real volume.** If the top seller moved fewer than 10 units in 30 days, say "sales signal is thin" instead of crowning a winner.
- **Research themes need recency and authority.** Prefer Vercel's own announcements over recaps.

Stating these limits in the report is part of the deliverable.

## Step 3: Synthesize exactly 5 candidates

Each candidate must:

1. Be inspired by a **specific** research finding (a named Vercel product or feature, not "developer culture").
2. Fit a category that's working in sales. If the signal is thin, label the category fit speculative.
3. Not duplicate the catalog (same product type plus theme); adjacent themes are fine.
4. Avoid known returns patterns. If a product has 3+ returns, do not recommend it or a close variant without explaining a concrete way the design or product addresses the return reason.

Rank by confidence. If only 3 candidates are strong, still produce 5 and label the speculative ones honestly. Never invent source findings, sales rows, return counts, or catalog gaps to fill the format. If an input is unavailable, disclose that and mark affected candidates speculative.

## Step 4: Render the report

Use this structure. Show today's date as `YYYY-MM-DD`. Every candidate must cite specific evidence in all four evidence lines; say "unavailable" where an input failed rather than implying there was no signal.

```markdown
# Product Ideation: {today's date}

## How these were chosen

{One paragraph: data windows, source counts, which inputs had thin signal.}

## Candidates

### 1. {Product name}

**Category:** {t-shirts | hoodies | mugs | hats | stationery | tech accessories}
**Pitch:** {One sentence.}
**Why:**

- _Research:_ {Specific Vercel theme + source}
- _Sales:_ {Specific row, "{Category} is #N ({units} units, ${revenue})"}
- _Returns:_ {"No clustered returns" OR "Pattern of N returns, addresses by …"}
- _Catalog gap:_ {Why this isn't already there}

### 2. ...

## Inputs summary

- **Sales window:** {from}–{to}, {N} products, top seller: {name} ({units} units)
- **Returns window:** {from}–{to}, {N} total, clustered: {list or "none"}
- **Research:** {N} sources, {N} themes
- **Catalog:** {N} products across {N} categories
```

If you produce ideas without citations, treat single-digit numbers as trends, or recommend duplicates, you've defeated the point of the skill.
