# Kampalo plugin for Grok Build

[![Kampalo MCP connector – tool definition quality and endpoint health on Glama](https://glama.ai/mcp/connectors/com.kampalo/kampalo/badges/score.svg)](https://glama.ai/mcp/connectors/com.kampalo/kampalo)

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

Set `KAMPALO_MCP_API_KEY` to your personal `kmp_…` key from Kampalo **Settings → API Keys** (Starter and Enterprise). On first tool use Grok calls `https://be.kampalo.com/mcp` with `Authorization: Bearer …`.

Every tool also needs a Kampalo `user_id` or `user_email`. Ask the user if neither is known. A personal key already binds your user, so you can omit both.

## Cursor

The same repo ships Cursor packaging next to the Grok files. Both use the same `skills/` and the same MCP server.

| Grok Build | Cursor |
| --- | --- |
| `.grok-plugin/plugin.json` | `.cursor-plugin/plugin.json` |
| `.mcp.json` (`${KAMPALO_MCP_API_KEY}`) | `mcp.json` (`${env:KAMPALO_MCP_API_KEY}`) |

1. Generate a key in Kampalo **Settings → API Keys**.
2. Set it in the environment Cursor starts from, then restart Cursor:

   ```powershell
   setx KAMPALO_MCP_API_KEY "kmp_..."
   ```

   ```bash
   export KAMPALO_MCP_API_KEY="kmp_..."   # in ~/.zshrc or ~/.bashrc
   ```

3. Install the plugin from this repo in Cursor. Check **Settings → MCP** shows `kampalo` connected, then ask: *Brief Google vs Meta in Kampalo.*

Without the plugin, the same server works from a project or user `~/.cursor/mcp.json` by copying the `kampalo` entry from `mcp.json`.

## Claude (claude.ai, Cowork, Claude Code)

The Claude plugin lives in [`claude/`](claude/) and is what the Claude directory lists. It uses OAuth: Claude asks you to sign in to Kampalo the first time, so there is no `KAMPALO_MCP_API_KEY` to set. Its skills are a copy of `skills/` without the `user_id` / `user_email` step, because the signed-in account is applied automatically. Keep both copies in sync when you edit a skill.

Install in Claude Code from this repo's marketplace (`.claude-plugin/marketplace.json` points at `./claude`):

```text
/plugin marketplace add Tekreign/kampalo-cursor-plugin
/plugin install kampalo@kampalo
```

Run `/mcp`, choose `kampalo`, and sign in to Kampalo when prompted.

## Network and credentials

| What | Value |
| --- | --- |
| MCP endpoint | `https://be.kampalo.com/mcp` (streamable HTTP) |
| Auth | `Authorization: Bearer ${KAMPALO_MCP_API_KEY}` |
| Secret | Personal `kmp_…` key from Kampalo **Settings → API Keys** (Starter and Enterprise). Scoped to your account, plan and role. |

The plugin talks only to that MCP host. It does not read local `.env` / SSH keys or send telemetry elsewhere.

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
kampalo-cursor-plugin/
├── .grok-plugin/plugin.json
├── .mcp.json
├── .cursor-plugin/plugin.json
├── mcp.json
├── .claude-plugin/plugin.json
├── .claude-plugin/marketplace.json
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

## License

MIT
