# Automation MCP tools

Server id: `kampalo`. Same permissions as the Kampalo app for the signed-in user.

Common: optional `client_id`. The signed-in account is applied automatically.

## Pause proposals

| Tool | Notes |
| --- | --- |
| `automate_list_actions` | Recent proposals / executions |
| `automate_propose_pause` | `campaign_id`, `platform` (`google_ads`\|`meta_ads`), optional `reason`, `google_customer_id`, `meta_ad_account_id` |
| `automate_propose_budget` | `campaign_id`, `platform` (`google_ads`\|`meta_ads`), `to_amount` (new daily budget, within ±20%), optional `reason`, `google_customer_id`, `meta_ad_account_id` |
| `automate_confirm_action` | `action_id` + `confirm=true` — pauses or applies the budget at the provider |
| `automate_dismiss_action` | `action_id` |

## Ads alerts (admin)

Notify after hourly sync. Metrics: `roas`, `cpa`, `spend_pace`. Operators: `lt`, `lte`, `gt`, `gte`. Platform: `all`, `google`, `meta`.

| Tool | Notes |
| --- | --- |
| `automate_list_alert_rules` | |
| `automate_upsert_alert_rule` | Omit `rule_id` to create |
| `automate_delete_alert_rule` | `rule_id` required |

## SEO / AEO alerts (admin, Starter+)

Metrics: `gsc_position`, `gsc_ctr`, `gsc_clicks`, `ai_sessions`. Never pauses spend.

| Tool | Notes |
| --- | --- |
| `automate_list_seo_alert_rules` | |
| `automate_upsert_seo_alert_rule` | |
| `automate_delete_seo_alert_rule` | |

## ROAS automation (admin)

Condition is only `roas_below`. Action is only `pause`. Create stays dry-run until `confirm_live=true`.

| Tool | Notes |
| --- | --- |
| `automate_list_automation_rules` | |
| `automate_upsert_automation_rule` | `roas_threshold`, `consecutive_days`, `min_spend`, `confirm_live` |
| `automate_delete_automation_rule` | |

## Reports

| Tool | Notes |
| --- | --- |
| `automate_generate_report` | JSON only. `report_type`, `platform`, optional dates / `range_preset` / `scope` (`brand`\|`client`\|`user`). Campaign list capped at 15. |
