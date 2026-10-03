# Skysay MCP

Connect an AI assistant to your existing [Skysay](https://skysay.ai) workspace to inspect and manage voice agents, phone numbers, calls, SMS and campaigns.

This repository distributes public connection configuration and metadata for Skysay's hosted MCP service. It does **not** contain the hosted application's source code, credentials, or customer data.

## Connect

- Endpoint: `https://api.skysay.ai/mcp`
- Transport: Streamable HTTP. No local server installation is required.
- Authentication: OAuth authorization-code flow with PKCE S256, or a scoped Skysay API key.
- [Setup and authorization guide](https://skysay.ai/docs/mcp)
- [Tool reference](https://skysay.ai/docs/tool-reference)
- [Scopes and permissions](https://skysay.ai/docs/scopes)

For an MCP client with remote OAuth support, add:

```json
{
  "mcpServers": {
    "skysay": {
      "type": "http",
      "url": "https://api.skysay.ai/mcp"
    }
  }
}
```

The assistant will open Skysay sign-in and workspace consent. Review the client, workspace and permissions before connecting. Revoke a connection from **Connected apps** on the workspace's API keys page.

For clients using API keys, follow the setup guide to supply the key locally as a bearer header. Never commit or publish a key.

## Capabilities and costs

Read workspace activity, agent configuration, phone numbers and call/campaign status. Supported configuration, calling, SMS and campaign actions require the corresponding permissions. Availability depends on account capabilities and service configuration. Calling, messaging, number provisioning, voice previews and simulations may incur charges under your Skysay account. See [pricing](https://skysay.ai/pricing).

For a fixed read-only connection, use `https://api.skysay.ai/mcp/read-only`. It exposes `account_overview`, `list_agents`, `get_agent`, `list_numbers` and `list_calls`, without recordings or transcripts. Its OAuth resource and tokens are separate from the full endpoint.

## Package contents

- `.mcp.json`: remote OAuth-capable MCP configuration, without credentials.
- `plugin.json`: portable display metadata for the connection package.
- `.claude-plugin/plugin.json` and `.grok-plugin/plugin.json`: client plugin metadata.
- `server.json`: proposed Official MCP Registry metadata. Inclusion in this repository does **not** mean registry publication or marketplace approval.

## Support

[Contact Skysay](https://skysay.ai/contact) · [Privacy](https://skysay.ai/privacy) · [Terms](https://skysay.ai/terms) · hello@skysay.ai

The hosted service is governed by Skysay's terms. Marketplace submission, marketplace approval and production service availability are separate.
