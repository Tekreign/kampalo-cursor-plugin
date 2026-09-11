# Kampalo plugin for Grok Build

[Grok Build](https://github.com/xai-org/plugin-marketplace) plugin that connects to Kampalo’s hosted FastMCP server. Grok can **read synced ads/SEO/GA4 data** and run the automations Kampalo already has: pause proposals, ads/SEO alerts, ROAS pause rules, and report JSON.

This plugin does not add product APIs. It wires Grok to `python manage.py run_mcp_server`:

- Read tools from `marketing/agents/suites.py` (synced Postgres)
- `automate_*` tools from `marketing/agents/automation_backend.py` (same services as `/api/marketing/actions/`, alert rules, automation rules, reports)

In-app Kai (LangGraph) stays **read-only**. Only this MCP catalog has `automate_*`.

## Installation

After the plugin is listed in the [xAI marketplace](https://github.com/xai-org/plugin-marketplace), in Grok Build run `/plugin`, search **Kampalo**, and install.

Until then, install from the plugin repo:

```text
grok plugin install Tekreign/kampalo-cursor-plugin
```

Set `KAMPALO_MCP_API_KEY` to the same secret as backend `MCP_API_KEY`. On first tool use Grok calls `https://be.kampalo.com/mcp` with `Authorization: Bearer …`.

Every tool also needs a Kampalo `user_id` or `user_email`. Ask the user if neither is known.

## Network and credentials

| What | Value |
| --- | --- |
| MCP endpoint | `https://be.kampalo.com/mcp` (streamable HTTP) |
| Auth | `Authorization: Bearer ${KAMPALO_MCP_API_KEY}` |
| Secret | Same as backend `MCP_API_KEY`. Required when the server has auth enabled (`DEBUG=False`). |
| Not this URL | `https://be.kampalo.com/api` — Django REST, not MCP |

The plugin talks only to that MCP host. It does not read local `.env` / SSH keys or send telemetry elsewhere.

Local Compose (`docker compose up`) exposes MCP at **http://127.0.0.1:8100/mcp**. To point Grok at it, override the server URL in `.mcp.json` for that session.

## What Grok can do

With MCP up and a Kampalo user who can mutate data (admin, or enterprise manager/marketer) for confirms. Alert/automation rule writes are **admin only**.

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

## Layout

```text
plugins/kampalo-grok-bot/
├── .grok-plugin/plugin.json
├── .mcp.json
├── skills/campaign-performance-brief/
├── skills/automate-kampalo-work/
├── assets/logo.svg
├── LICENSE
└── README.md
```

## Skills

| Skill | When |
| --- | --- |
| `campaign-performance-brief` | Read synced Google Ads, Meta Ads, Search Console, GA4, Page/IG |
| `automate-kampalo-work` | Propose/confirm pauses, alerts, ROAS rules, report JSON |

## Example prompts

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

## xAI marketplace entry (when submitting)

Remote source, SHA-pinned. Do not vendor files into `xai-org/plugin-marketplace`. After pushing this folder to a public repo:

```bash
git ls-remote https://github.com/Tekreign/kampalo-cursor-plugin.git HEAD
```

Add one object to their `.grok-plugin/marketplace.json`:

```json
{
  "name": "kampalo",
  "description": "Kampalo ads and SEO workspace for Grok Build. Brief synced Google Ads, Meta Ads, GA4, and Search Console; propose and confirm campaign pauses; manage ads/SEO alerts and ROAS pause rules; generate report JSON.",
  "category": "development",
  "source": {
    "source": "url",
    "url": "https://github.com/Tekreign/kampalo-cursor-plugin.git",
    "sha": "<40-char lowercase commit sha>"
  },
  "homepage": "https://app.kampalo.com",
  "keywords": ["kampalo", "kampalo ads", "kampalo google ads", "kampalo meta ads", "kampalo kai"],
  "domains": ["kampalo.com", "app.kampalo.com", "be.kampalo.com"]
}
```

Then in that fork: `python3 scripts/generate-plugin-index.py` and `python3 scripts/validate-catalog.py`. xAI flags branded plugins sourced from a personal GitHub account — move the plugin repo under a Kampalo org before the PR if possible.

## License

MIT
