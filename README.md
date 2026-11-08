# Skysay phone skills and MCP plugin

Give your agent a real phone number. With Skysay, an agent can check coverage, get a number, place AI voice calls, send SMS and read call results through Skysay's hosted MCP server. 100+ countries covered; channels vary by country and number type.

## What's included

| Component | Path | Purpose |
| --- | --- | --- |
| Manifest | `.cursor-plugin/plugin.json` | Plugin metadata |
| MCP server | `mcp.json` | Remote Skysay MCP at `https://api.skysay.ai/mcp` |
| Shared skill | `skills/skysay-phone/SKILL.md` | Set up Skysay, prepare custom phone tasks, call, text and verify results |
| Restaurant skill | `skills/skysay-book-restaurant/SKILL.md` | Book a table within the user's dates, times and permitted alternatives |
| Business hours skill | `skills/skysay-confirm-business-hours/SKILL.md` | Confirm a branch's hours for a date, including holidays and service cutoffs |
| Logo | `assets/logo.svg` (`assets/logo.png` 512×512) | Marketplace icon |

The package contains instructions, plugin metadata and a remote MCP connection. Skysay runs the calls and stores the transcripts. It requires no local phone bridge or speech service.

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

Connecting is free. Phone numbers, phone calls and SMS use purchased Skysay funds. The one-time $5 welcome credit covers only browser testing inside Skysay. The skills confirm the task and cost basis before a call, text, number request or top-up, and reuse explicit approval of the same complete plan. This task-approval step is assistant behavior; OAuth consent and the server's workspace readiness, funding and calling controls do not verify approval of each task. [Current pricing](https://skysay.ai/pricing)

## Phone tasks

The assistant builds a brief for the chosen voice agent and sends it with `create_outbound_call.runtime_config`. Each call keeps its own instructions and destination, with an idempotency key when the client's tool schema accepts it. An uncertain submission is reconciled before another call is attempted. The assistant then reads that call's result and transcript to establish whether the requested task succeeded. A completed call alone does not confirm a booking.

`skysay-phone` handles custom tasks. The restaurant and business-hours skills add the details each task must collect and verify. All three skills install together through this plugin's `skills` directory. A read-only connector can inspect results and requires a separately authorized full connection to place a call.

For a client with a skills installer, run this from the package source, or use the repository URL after the skill changes merge:

```sh
npx skills add ./
```

Select `skysay-phone`, `skysay-book-restaurant` and `skysay-confirm-business-hours` together, so their shared references are available. Connect the hosted Skysay MCP in that client using OAuth. For manual installation, keep these three folders beside each other in the client's skill directory.

In Codex, the new skills become available in a new conversation after installation. The package includes `agents/openai.yaml` display metadata. Plugin installation and authentication depend on the client's supported configuration.

## Example prompts

- "Which Skysay number types are available in Germany, and what do they cost?"
- "Check my UK number's outbound readiness, prepare one short test call to the number I give you, and show me the cost before dialling."
- "Check whether my number can send SMS and draft this text for my approval: …"
- "Show the status and transcript of my last Skysay call."
- "Use skysay-book-restaurant to book this restaurant tomorrow at 19:00 for two. You may accept 18:30–19:30. Show me the call plan first."
- "Use skysay-confirm-business-hours to call this branch and check its holiday hours on 25 December, including the kitchen's last orders."
- "Use skysay-phone to ask the repair shop whether my order is ready. Give the order number I provided and return any price change to me."

Phone task instructions are original Skysay workflows inspired by [PhoneZero](https://github.com/function1st/PhoneZero). They use Skysay's hosted agent and call evidence through the existing MCP connection.

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
