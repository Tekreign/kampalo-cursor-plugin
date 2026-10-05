# Kampalo

The `kampalo` MCP server reads a user's synced Kampalo marketing data and runs Kampalo automations. Gemini asks the user to sign in to Kampalo the first time a tool runs (OAuth). Kampalo Starter or Enterprise is required.

- Use the `campaign-performance-brief` skill for Google Ads, Meta Ads, GA4, Search Console and Facebook Page / Instagram questions.
- Use the `automate-kampalo-work` skill to propose or confirm pauses, create alerts and ROAS rules, or generate a report.
- The signed-in account is applied automatically, so you can omit `user_id` and `user_email`.
- Never invent spend, ROAS, conversions or rankings. If a tool fails or returns no rows, say so.
- Writes are proposals until the user explicitly confirms them.
