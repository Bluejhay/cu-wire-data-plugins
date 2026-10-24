# Sloe Data Plugins

Plugin package and connection instructions for Sloe Data.

## Connect Codex now (no Git required)

The hosted read-only MCP server works without installing this plugin marketplace.
In Terminal on the computer running Codex, use the [Codex CLI](https://learn.chatgpt.com/docs/codex/cli):

```bash
codex mcp add sloe-data --url https://cu-wire-mcp.vercel.app/data-mcp
codex mcp login sloe-data
```

Approve the browser connection while signed in to a Sloe Data account with
Data Access. Restart the Codex desktop app or start a new task, then ask Codex to
use `sloedata_get_industry_summary`. Existing tasks can retain an old tool
list. This route registers the read-only research tools directly; it does not add
the optional plugin's skill and marketplace listing. Neither route grants a
Data Access license by itself.

Sloe Data is not yet in the built-in public plugin catalog. The Git
marketplace below is an optional tester/developer path. It uses system Git, so
macOS Git or Xcode setup can block installation even while the hosted connector
is healthy. The direct MCP route above avoids that dependency.

## Claude Code Tester Install

```bash
claude plugin marketplace add CUWireDatav2/cu-wire-data-plugins
```

```bash
claude plugin install sloe-data@sloe-data
```

Then connect the account. Installation alone leaves the connector
unauthenticated:

1. Run `/mcp` in Claude Code.
2. Select `sloe-data`.
3. Choose **Authenticate**, sign in to Sloe Data in the browser, and approve
   read-only access.

The first Sloe Data tool call also prompts for authorization, because the
server answers an unauthenticated request with a `401` challenge that names its
OAuth server.

If tools do not appear after connecting, restart Claude Code or open a new
session.

## Optional Codex plugin install

```bash
codex plugin marketplace add CUWireDatav2/cu-wire-data-plugins
```

```bash
codex plugin add sloe-data@sloe-data
```

```bash
codex mcp login sloe-data
```

After approving the browser connection, start a new Codex task before testing.
Already-open tasks can keep a stale MCP tool list.

If the Sloe Data skill appears but the `sloedata_*` tools do not, re-run
`codex mcp login sloe-data`, approve the browser connection, then start a new
task or restart the desktop app.

## Troubleshooting

**Tools return "connector is not authenticated"**: the account is not connected
yet. Use the connect step for your assistant above. Do not set
`CUWIREDATA_API_KEY` for the public plugin path.

**Tools return "Invalid or revoked API key"**: the connected account's key is
revoked or not licensed for Data Access. Contact info@thecreditunionwire.com.

**The skill loads but no `sloedata_*` tools exist**: the client is holding a
stale tool list. Reconnect, then start a new session or task.
