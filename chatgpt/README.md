# Kampalo plugin for ChatGPT

ChatGPT package for Kampalo's hosted MCP server (`https://be.kampalo.com/mcp`). Same skills and server as the Claude plugin in `../claude`.

Users connect with **OAuth**: ChatGPT registers itself, the user signs in on Kampalo's page and clicks **Allow access**. No API key is needed. Requires Kampalo Starter or Enterprise.

| File | Purpose |
| --- | --- |
| `plugin.json` | Agent Plugins manifest with the `com.openai` listing, review test cases and publication settings |
| `mcp.json` | The Kampalo MCP server (streamable HTTP) |
| `skills/` | Same skills as the Claude plugin |
| `assets/` | `logo.png` (1024px) and `icon.png` (256px) |

## Build the ZIP

```bash
cd chatgpt && zip -r ../dist/kampalo-chatgpt.zip . -x README.md
```

Upload it at [platform.openai.com/plugins](https://platform.openai.com/plugins). Before uploading, set `demo_recording_url` and check `publication.countries` in `plugin.json`. Reviewer credentials go in the dashboard form, never in the ZIP.

## Test before submitting

ChatGPT → Settings → Security and login → **Developer mode**, then Plugins → **+** → Connection → `https://be.kampalo.com/mcp`.
