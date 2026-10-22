# Skysay plugin for Cursor and Grok Bot

Give your agent a real phone number. With Skysay, an agent can check coverage, get a number, place AI voice calls, send SMS and read call results through Skysay's hosted MCP server. 100+ countries covered; channels vary by country and number type.

## What's included

| Component | Path | Purpose |
| --- | --- | --- |
| Manifest | `.cursor-plugin/plugin.json` | Plugin metadata |
| MCP server | `mcp.json` | Remote Skysay MCP at `https://api.skysay.ai/mcp` |
| Skill | `skills/skysay-phone/SKILL.md` | How to get a number, call, text and check results safely, with approval before anything that costs money or contacts someone |
| Logo | `assets/logo.svg` (`assets/logo.png` 512×512) | Marketplace icon |

No hooks, scripts, install steps, local processes or telemetry. No credentials are stored in this repository.

## Connect

1. Install **Skysay** from the marketplace (Cursor: Customize → Plugins; Grok Bot: Connect apps).
2. When asked, authenticate. Skysay opens its own sign-in page (verified email code, or Google/GitHub). Review the client, workspace and permissions on the consent screen, then choose **Allow access**.
3. Ask the agent: "Use Skysay account_overview and summarise my workspace. Do not place calls or send SMS."

| Setting | Value |
| --- | --- |
| Server URL | `https://api.skysay.ai/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth 2.0 authorization code with PKCE S256; dynamic client registration and Client ID Metadata Documents supported; no client secret |
| Read-only alternative | `https://api.skysay.ai/mcp/read-only` (five read tools, no calls or writes) |

The default grant is read-only: workspace, agents, call metadata, coverage, numbers, onboarding and policy evaluation. Calling (`calls:write`), SMS (`sms:send`, `sms:read`), transcripts (`recordings:read`), number requests (`numbers:write`) and top-ups (`wallet:write`) need extra consent. Revoke access any time under **Connected apps** on the API keys page in your Skysay workspace.

## Costs

Connecting is free. Phone numbers, phone calls and SMS use purchased Skysay funds; the one-time $5 welcome credit only covers browser testing inside Skysay. See https://skysay.ai/pricing. The skill shows the expected cost and waits for your approval before any call, text, number request or top-up. That approval step is the assistant's behaviour, not something OAuth enforces, so review consent scopes carefully.

## Example prompts

- "Which Skysay number types are available in Germany, and what do they cost?"
- "Check my UK number's outbound readiness, prepare one short test call to the number I give you, and show me the cost before dialling."
- "Check whether my number can send SMS and draft this text for my approval: …"
- "Show the status and transcript of my last Skysay call."

## Docs and support

- Grok Bot setup: https://skysay.ai/docs/grok-bot
- MCP overview: https://skysay.ai/docs/mcp
- Tool reference: https://skysay.ai/docs/tool-reference
- Scopes: https://skysay.ai/docs/scopes
- Support: https://skysay.ai/contact · hello@skysay.ai
- Privacy: https://skysay.ai/privacy · Terms: https://skysay.ai/terms

## Other files in this repository

- `.mcp.json`: the same remote OAuth MCP configuration for clients that read `.mcp.json`.
- `plugin.json`: portable display metadata for the connection package.
- `.claude-plugin/plugin.json` and `.grok-plugin/plugin.json`: client plugin metadata.
- `server.json`: proposed Official MCP Registry metadata. Inclusion in this repository does **not** mean registry publication or marketplace approval.

Marketplace submission, marketplace approval and production service availability are separate.

## License

[MIT](LICENSE) for the files in this repository. Use of the hosted Skysay service is governed by Skysay's terms. Skysay is operated by Herous Futurus OÜ.
