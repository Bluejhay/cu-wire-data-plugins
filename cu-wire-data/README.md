# CU Wire Data Plugin

Read-only plugin for licensed CU Wire Data access in Claude Code, Codex, and
other MCP clients.

Installing this plugin does not grant CU Wire Data access. Users need a licensed
CU Wire Data account.

## Connect Codex now (no Git required)

The hosted read-only MCP server works without installing the plugin marketplace.
In Terminal on the computer running Codex, use the [Codex CLI](https://learn.chatgpt.com/docs/codex/cli):

```bash
codex mcp add cu-wire-data --url https://cu-wire-mcp.vercel.app/data-mcp
codex mcp login cu-wire-data
```

Approve the browser connection while signed in to a CU Wire Data account with
Data Access. Restart the Codex desktop app or start a new task, then ask Codex to
use `cuwiredata_get_industry_summary`. Existing tasks can retain an old tool
list. This direct route exposes the read-only research tools without the optional
plugin skill. Neither route grants a Data Access license by itself.

CU Wire Data is not yet in the built-in public plugin catalog. The Git
marketplace install below is an optional tester/developer route. It uses system
Git, so macOS Git or Xcode setup can block it even while the hosted connector is
healthy. The direct MCP route above avoids that dependency.

## Tester/Developer Setup

### Claude Code

```bash
claude plugin marketplace add CUWireDatav2/cu-wire-data-plugins
```

```bash
claude plugin install cu-wire-data@cu-wire-data
```

Then run `/mcp`, select `cu-wire-data`, and choose **Authenticate**. Sign in to
CU Wire Data in the browser and approve read-only access.

### Optional Codex plugin

```bash
codex plugin marketplace add CUWireDatav2/cu-wire-data-plugins
```

```bash
codex plugin add cu-wire-data@cu-wire-data
```

```bash
codex mcp login cu-wire-data
```

After approval, start a new Codex task before testing. Already-open tasks can
keep a stale MCP tool list and may not expose `cuwiredata_get_industry_summary`
until the task or session refreshes.

## Authentication

Both paths use the same browser OAuth flow. The assistant opens a CU Wire Data
sign-in and approval page, the user approves read-only access, and the client
returns holding a scoped connector token. No API key is pasted into the
assistant, chat, URLs, screenshots, or repository files.

If the skill is visible but the `cuwiredata_*` tools are missing, reconnect
through your assistant's MCP connect flow and start a new session or task. Do
not set `CUWIREDATA_API_KEY`; that is a legacy manual path, not the public
plugin path.

The plugin connects to:

```text
https://cu-wire-mcp.vercel.app/data-mcp
```

## Tools

- `cuwiredata_get_industry_summary`
- `cuwiredata_search_institutions`
- `cuwiredata_get_institution`
- `cuwiredata_get_history`
- `cuwiredata_compare_institutions`
- `cuwiredata_get_peers`
- `cuwiredata_get_branches`
- `cuwiredata_get_industry_trends`
- `cuwiredata_get_mergers`
- `cuwiredata_get_vendor_relationships`
- `cuwiredata_list_datasets`
- `cuwiredata_get_dataset`
- `cuwiredata_get_hmda`
- `cuwiredata_get_institution_profile`
- `cuwiredata_get_call_report`

The plugin does not include customer credentials.

## Boundaries

- Read-only.
- No admin/editorial tools.
- No public/free default key.
- No keys in prompts, URLs, docs, tickets, screenshots, or repositories.
- No public NCUA, web, cached, model-memory, or other fallback answer when the licensed CU Wire Data connector is unauthenticated or fails.
- No bulk export, resale, redistribution, or warehouse sync through the connector.
- Assistant answers should preserve the returned period, as-of date, and source.
