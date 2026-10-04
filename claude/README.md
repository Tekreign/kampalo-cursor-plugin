# Kampalo for Claude

Ask Claude about your marketing performance in [Kampalo](https://kampalo.com): Google Ads, Meta Ads, GA4, Search Console, Facebook Page, Instagram and Shopify. Claude can also propose campaign pauses and budget changes, and manage alert and ROAS pause rules. Nothing changes in Google Ads or Meta until you confirm.

![Kampalo](assets/logo.svg)

## What you need

- A Kampalo **Starter** or **Enterprise** plan
- At least one platform connected in Kampalo (Google Ads, Meta Ads, GA4, Search Console or Shopify)

## Connect

Install the plugin, then use any Kampalo skill or ask about your campaigns. Claude asks you to connect Kampalo the first time: sign in with your Kampalo email and password and select **Allow**. There is no API key to copy.

To disconnect, remove the Kampalo connector in Claude's settings.

## Skills

| Skill | What it does |
| --- | --- |
| `campaign-performance-brief` | Answers questions about spend, ROAS, campaigns, search queries, GA4 traffic, organic social and Shopify from your synced Kampalo data |
| `automate-kampalo-work` | Proposes pauses and budget changes, runs them only after you confirm, manages alert and ROAS pause rules, and builds performance reports |

## Try asking

- "Compare Google and Meta for the last 30 days."
- "Which Google Ads campaigns have ROAS under 1.5?"
- "What are my top Search Console queries this month?"
- "Propose pausing my worst Meta campaign."
- "Alert me if ROAS drops below 2."

## What the plugin connects to

The plugin contains two skills and one remote MCP server, `https://be.kampalo.com/mcp`, run by Kampalo. It runs no local code and sends data nowhere else.

- **Reads** return the marketing data Kampalo has already synced for your account. Tools do not call Google, Meta or Shopify directly.
- **Writes** change Kampalo records: proposals, alert rules, ROAS pause rules. A confirmed pause or budget change is sent from Kampalo to Google Ads or Meta, and only when you confirm it.
- Access uses OAuth and is limited to your Kampalo account and your role's permissions.

## Privacy and support

- Privacy policy: https://kampalo.com/privacy
- Support: connect@tekreign.com

## License

MIT. See [LICENSE](LICENSE).
