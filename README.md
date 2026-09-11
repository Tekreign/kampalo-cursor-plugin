# Kampalo Grok Bot / Cursor plugin

Grok Bot (and Cursor) can **read Kampalo data and run the automations Kampalo already has**: pause proposals, ads/SEO alerts, ROAS pause rules, and report JSON.

Built from the [Grok Bot plugin walkthrough](https://www.usenotra.com/blog/how-to-create-a-grok-bot-plugin) and Cursor’s [plugin](https://cursor.com/docs/plugins) / [MCP](https://cursor.com/docs/mcp) docs.

This plugin does **not** add new product APIs. It connects to FastMCP (`python manage.py run_mcp_server`) which now exposes:

- Read tools from `marketing/agents/suites.py` (synced DB)
- `automate_*` tools from `marketing/agents/automation_backend.py` (same services as `/api/marketing/actions/`, alert rules, automation rules, reports)

## What Grok Bot can do

With MCP up and a Kampalo `user_id` or email:

1. Brief Google vs Meta, SEO, GA4, and organic Page/IG from synced stats
2. Propose pausing a weak selected campaign
3. Confirm that proposal (`confirm=true`) — writes `PAUSED` to Google or Meta
4. Create ads alerts (ROAS / CPA / spend pace) and SEO/AEO alerts
5. Create a dry-run ROAS automation rule; enable live only with `confirm_live=true`
6. Generate a performance report JSON (no PDF bytes over MCP)

## What it cannot do

- Connect OAuth, change budgets, create campaigns, or write Shopify/TikTok
- Pause without a catalog-backed proposal + explicit confirm
- Act as a view-only user for confirms or rule admin
- Invent metrics if MCP is down or the DB has no rows

In-app Kai (LangGraph) stays **read-only**. Only this MCP catalog has `automate_*`.

## Layout

```text
plugins/kampalo-grok-bot/
├── .cursor-plugin/plugin.json
├── skills/campaign-performance-brief/
├── skills/automate-kampalo-work/
├── mcp.json
├── assets/logo.svg
└── README.md
```

## Prerequisites

1. Backend + Postgres with synced selected campaigns (`docker compose up` includes `mcp` on **http://127.0.0.1:8100/mcp**).
2. `MCP_API_KEY` in `backend/.env` if the port is reachable beyond localhost.
3. A user who can mutate data (admin, or enterprise manager/marketer) for confirms. Alert/automation rule writes are **admin only**.

There is no public MCP hostname in this repo. Grok Bot needs a host-reachable URL of `run_mcp_server`. Do not point at `https://be.kampalo.com` unless that host serves FastMCP at `/mcp`.

## Configure

| Variable | Example |
| --- | --- |
| `KAMPALO_MCP_URL` | `http://127.0.0.1:8100/mcp` |
| `KAMPALO_MCP_API_KEY` | same as `MCP_API_KEY` |

## Test locally

Junction/symlink this folder to `~/.cursor/plugins/local/kampalo`, reload Cursor, start `mcp`.

### Test tasks

**Read**

> Use my Kampalo email `you@company.com`. Brief Google vs Meta. Which campaigns have the worst ROAS?

**Propose (no live pause)**

> Propose pausing the worst ROAS Google campaign. Do not confirm yet.

Expect `automate_propose_pause` and a pending `action_id`. The live campaign status must stay ENABLED.

**Confirm (live pause)**

> Confirm pause for action `<id>`.

Expect `automate_confirm_action` with `confirm=true`. Campaign becomes PAUSED (needs valid Google/Meta tokens).

**Watchdog**

> Create a dry-run rule: pause Google campaigns if ROAS stays below 1 for 3 days with at least $10 spend. Do not enable live.

Expect `automate_upsert_automation_rule` with `dry_run=true`, `is_active=false`.

**Failure**

Stop MCP and ask again. The bot must not claim a pause or a saved rule.

## Submit

Push a public repo of this plugin folder and submit at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).
