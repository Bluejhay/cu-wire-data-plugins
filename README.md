# CU Wire Data Plugins

Plugin package and connection instructions for CU Wire Data.

## Connect Codex now (no Git required)

The hosted read-only MCP server works without installing this plugin marketplace.
In Terminal on the computer running Codex, use the [Codex CLI](https://learn.chatgpt.com/docs/codex/cli):

```bash
codex mcp add cu-wire-data --url https://cu-wire-mcp.vercel.app/data-mcp
codex mcp login cu-wire-data
```

Approve the browser connection while signed in to a CU Wire Data account with
Data Access. Restart the Codex desktop app or start a new task, then ask Codex to
use `cuwiredata_get_industry_summary`. Existing tasks can retain an old tool
list. This route registers the read-only research tools directly; it does not add
the optional plugin's skill and marketplace listing. Neither route grants a
Data Access license by itself.

CU Wire Data is not yet in the built-in public plugin catalog. The Git
marketplace below is an optional tester/developer path. It uses system Git, so
macOS Git or Xcode setup can block installation even while the hosted connector
is healthy. The direct MCP route above avoids that dependency.

## Claude Code Tester Install

```bash
claude plugin marketplace add CUWireDatav2/cu-wire-data-plugins
```

```bash
claude plugin install cu-wire-data@cu-wire-data
```

Then connect the account. Installation alone leaves the connector
unauthenticated:

1. Run `/mcp` in Claude Code.
2. Select `cu-wire-data`.
3. Choose **Authenticate**, sign in to CU Wire Data in the browser, and approve
   read-only access.

The first CU Wire Data tool call also prompts for authorization, because the
server answers an unauthenticated request with a `401` challenge that names its
OAuth server.

If tools do not appear after connecting, restart Claude Code or open a new
session.

## Optional Codex plugin install

```bash
codex plugin marketplace add CUWireDatav2/cu-wire-data-plugins
```

```bash
codex plugin add cu-wire-data@cu-wire-data
```

```bash
codex mcp login cu-wire-data
```

After approving the browser connection, start a new Codex task before testing.
Already-open tasks can keep a stale MCP tool list.

If the CU Wire Data skill appears but the `cuwiredata_*` tools do not, re-run
`codex mcp login cu-wire-data`, approve the browser connection, then start a new
task or restart the desktop app.

## Troubleshooting

**Tools return "connector is not authenticated"**: the account is not connected
yet. Use the connect step for your assistant above. Do not set
`CUWIREDATA_API_KEY` for the public plugin path.

**Tools return "Invalid or revoked API key"**: the connected account's key is
revoked or not licensed for Data Access. Contact data@cuwiredata.com.

**The skill loads but no `cuwiredata_*` tools exist**: the client is holding a
stale tool list. Reconnect, then start a new session or task.
