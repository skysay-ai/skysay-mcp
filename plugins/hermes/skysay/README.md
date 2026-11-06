# Skysay for Hermes

Inspect your Skysay workspace, voice agents, phone numbers and call activity from Hermes. This connector is read-only: it cannot place calls, send SMS, buy numbers or change workspace settings. The separate [Skysay platform](https://skysay.ai) offers phone-number coverage in 100+ countries; availability varies by country and number type.

## Connect

1. Install **Skysay** from the Hermes Marketplace. Installation adds the workflow skill and declares the remote server; it does not sign you in.
2. In the same Hermes profile, run the following once. The command configures OAuth for the same read-only server, not a second connection.

   ```sh
   hermes mcp add skysay-readonly --url https://api.skysay.ai/mcp/read-only --auth oauth
   ```

3. Sign in on Skysay, choose the workspace you want Hermes to access, and review the permissions before allowing access. Choose which read tools to enable in Hermes. Check the connection with `hermes mcp test skysay-readonly`, then open a new Hermes chat.

Try: “Using only my Skysay read-only connection, summarize my workspace and list its voice agents. Don't include personal phone numbers.”

### If the browser approves but sign-in fails

If Hermes reports `invalid_grant: unknown authorization code`, close the old authorization tabs. In the active Hermes profile's `config.yaml`, add or update this entry under `mcp_servers` without replacing your other settings. This uses dynamic client registration instead of Hermes's default client metadata document:

```yaml
mcp_servers:
  skysay-readonly:
    url: https://api.skysay.ai/mcp/read-only
    auth: oauth
    enabled: true
    oauth:
      cimd: false
```

Run `hermes mcp login skysay-readonly`, then use only the newest authorization tab and check the selected Skysay workspace before granting access. This fallback uses the same read-only endpoint and does not require an API key or client secret.

To switch workspaces, revoke the old grant in Skysay's Connected apps, sign in to the intended workspace, then run `hermes mcp login skysay-readonly` again.

## Access and data

- Hermes sends tool requests to `https://api.skysay.ai/mcp/read-only` over HTTPS. Results can contain workspace data and are visible to your configured Hermes model. Connect only a workspace you're authorized to share with that model.
- Skysay advertises `account:read`, `agents:read`, `calls:read` and `numbers:read` for this endpoint. Hermes also requests `offline_access` so it can renew the connection. Skysay enforces the endpoint's tool restrictions; the skill is guidance, not an access-control layer.
- The plugin adds no executable code, background process, dependencies or telemetry. Hermes manages its own OAuth credentials; you don't paste credentials into chat or this plugin.
- To disconnect, revoke the Hermes grant in Skysay's Connected apps and run `hermes mcp remove skysay-readonly`. Uninstalling the plugin removes its bundled declaration and skill; uninstalling alone does not remove the OAuth entry or revoke access.

[Skysay MCP documentation](https://skysay.ai/docs/mcp) · [Support](https://skysay.ai/contact) · [Privacy](https://skysay.ai/privacy) · [Terms](https://skysay.ai/terms)
