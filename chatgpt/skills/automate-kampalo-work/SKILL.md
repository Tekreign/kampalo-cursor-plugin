---
name: automate-kampalo-work
description: Automate Kampalo marketing work from synced data — propose or confirm campaign pauses and budget changes, manage alert and ROAS automation rules, and generate reports. Use when the user asks to act on Kampalo, set alerts, pause weak campaigns, change a budget, schedule rules, or generate a report.
---

# Automate Kampalo work

Use Kampalo MCP tools (`kampalo`) to **read data first**, then take the actions Kampalo already supports. Do not invent connect, campaign-creation, or Shopify write APIs.

Read metrics with the campaign-performance tools. Writes use `automate_*` only. Catalog: [tools.md](tools.md)

If Kampalo isn't connected, ask the user to connect it and sign in. If a tool returns `error`, say so. Do not claim a campaign was paused or a rule was saved.

## Identity

The signed-in Kampalo account is applied automatically. Do not ask for or pass `user_id` / `user_email`. Pass `client_id` only when the user names an enterprise brand.

## What you can automate (real product)

| Job | How |
| --- | --- |
| Find weak spend / ROAS | `google_ads_get_campaigns` / `meta_ads_get_campaigns` (`sort_by`: `roas` or `spend`) |
| Propose a pause | `automate_propose_pause` — creates a pending `CampaignActionLog`. Does **not** pause live. |
| Propose a budget change | `automate_propose_budget` — pending daily-budget change, capped at 20% of the current budget. Does **not** change live. |
| Pause / re-budget live | `automate_confirm_action` with `action_id` **and** `confirm=true` after the user explicitly agrees |
| Cancel a proposal | `automate_dismiss_action` |
| Notify on bad ROAS/CPA | `automate_upsert_alert_rule` (admin). Hourly sync evaluates; notify only. |
| Notify on SEO / AI referrals | `automate_upsert_seo_alert_rule` (admin, Starter+) — never pauses ads |
| Auto-pause if ROAS stays low | `automate_upsert_automation_rule` — defaults `dry_run=true`, `is_active=false`. Live enable needs `confirm_live=true` |
| Performance report | `automate_generate_report` — JSON from synced DB, not PDF |

## Hard rules

1. **Read before write.** Propose only campaigns that appear in ads tools and the selected catalog.
2. **Pauses and budget changes are two steps.** Propose → show campaign name, platform, reason (and old/new budget), `action_id` → wait for the user to confirm → `confirm=true`. Never confirm in the same turn you proposed unless they already said “do it now”.
3. **View-only users** can read and may get proposals; they cannot confirm or manage rules. Surface the error text.
4. **Live automation** (`is_active=true` or `dry_run=false`) only after the user clearly asks to turn the rule on, then `confirm_live=true`.
5. **Platform names:** pause tools use `google_ads` / `meta_ads`. Alert and automation rules use `all` / `google` / `meta`.
6. **Out of scope:** connecting platforms, budget changes above the 20% cap, create campaigns, hide comments, Shopify writes, TikTok, inventing thresholds the user did not give.

## Default workflow

1. Resolve optional `client_id`.
2. `overview_get_account`, then the ads/SEO tools that match the ask.
3. Recommend from numbers. If they want action:
   - one-off pause or budget change → propose → confirm
   - ongoing watch → alert rule
   - ongoing pause → automation rule in dry-run first
   - share a pack → generate report
4. After writes, list the saved `id` and status (`proposed`, `executed`, `dry_run`).

## Answer

Name the campaigns and rule ids the tools returned. Do not pad with generic marketing playbooks. Do not mention internal tool JSON unless asked.
