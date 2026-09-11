---
name: campaign-performance-brief
description: Brief Kampalo campaign and marketing performance from synced database tools. Use when the user asks about Google Ads, Meta Ads, ROAS, spend, Search Console, GA4, Facebook Page or Instagram organic insights, or to compare platforms in Kampalo.
---

# Campaign performance brief

Read metrics only from the Kampalo MCP server (`kampalo`). Tools wrap synced Postgres. They do not call Google, Meta, or Shopify live APIs.

If the connector is missing, unauthorized, or a tool returns an error, say so. Do not invent spend, ROAS, conversions, or rankings.

## Scope

Answer only about Kampalo-connected marketing services:

- Google Ads
- Meta Ads
- Search Console / Lighthouse / page metadata
- GA4
- Meta organic (Facebook Page + Instagram)
- Cross-platform ads comparison

Refuse general knowledge, creative writing, and unrelated tools. There are **no** Shopify or TikTok MCP tools on the read catalog.

To pause campaigns, set alerts, enable ROAS rules, or generate a report, use the `automate-kampalo-work` skill and `automate_*` tools. Do not invent other write APIs.

Tool names and parameters: [tools.md](tools.md)

## Before calling tools

1. Every tool needs `user_id` (Django user pk) **or** `user_email`. Ask if neither is known. Do not guess another user's id.
2. Pass `client_id` only when the user names an enterprise brand/tenant. Otherwise omit it so the server uses the user's authorized default.
3. Dates: pass `date_from` / `date_to` / `days` / `range_preset` **only if the user asked for a range**. If they did not, omit date args so tools use all stored stats for the account.

## Workflow

1. Call `overview_get_account` (or `meta_insights_get_overview`) to see what is connected.
2. Route to the smallest tool set that answers the question:

| User asks about | Tools |
| --- | --- |
| Google vs Meta / which platform | `meta_insights_compare_platforms` |
| Google Ads ROAS, spend, campaigns | `google_ads_get_summary`, then `google_ads_get_campaigns` (`sort_by`: `roas` or `spend`). Add `google_ads_get_trends` only for time series. |
| Meta Ads | `meta_ads_get_summary`, then `meta_ads_get_campaigns` |
| Search / SEO / queries | `search_get_metrics`, `search_get_top_queries`. Add Lighthouse or metadata only if asked. |
| GA4 traffic / revenue | `analytics_get_ga4_metrics` |
| AI/AEO referrals | `analytics_get_ai_referrals` |
| Organic landing ROI | `analytics_get_organic_roi` |
| Checkout funnel | `analytics_get_checkout_funnel` |
| Paid vs organic queries | `search_get_paid_organic` |
| Facebook Page / Instagram organic | `meta_organic_get_overview` first, then page or IG insight/content tools |

3. Use `activity_only: true` on ads campaign/summary tools when the user wants active campaigns only.
4. Prefer `sort_by: roas` for “best ROAS” and `sort_by: spend` for “where is money going”.

## Answer

- Lead with the numbers the tools returned (currency as given).
- Name campaigns, queries, or pages from the payload. Do not pad with generic marketing advice.
- Empty synced data → say the DB has no rows for that range/account, not that performance is zero unless the tool summary is explicitly zero.
- All-zero summaries may be marked empty by the backend — treat them as empty, not as a successful campaign.
- Do not mention suite ids, tool JSON, or model names in the user-facing answer unless the user asks how you fetched the data.
