# Sloe Data

Read-only access to licensed Sloe Data research on U.S. credit unions, from
Claude, Claude Code, Codex, and other MCP clients.

Ask about any federally insured credit union or the whole industry: Call Report
financials and history, peer benchmarking, side-by-side comparisons, branches,
mergers, CU acquisitions of banks, CUSO and vendor relationships, HMDA mortgage
lending, regulatory and enforcement data, leadership, fees, rankings and
geographic markets. Answers keep the reporting period and source attached.

Installing this plugin does not grant data access. Any paid Sloe Data plan
includes it: Individual, Organization or Data Access. See
[sloedata.com/pricing](https://sloedata.com/pricing).

## What the plugin contains

- A connection to the hosted, read-only Sloe Data MCP server at
  `https://mcp.sloedata.com/data-mcp`.
- One skill that tells the assistant how to use the tools, keep period and
  source context, and stop instead of guessing when the connection fails.

It runs no local code, hooks, or scripts, and contains no credentials.

## Connect your account

The connector uses browser OAuth. The first time a Sloe Data tool runs, the
assistant opens a Sloe Data sign-in page. Approve read-only access and you
return to the assistant holding a scoped connector token. No API key is ever
pasted into the chat.

- **Claude Code**: run `/mcp`, select `sloe-data`, choose **Authenticate**,
  and approve access in the browser. Start a new session if the tools do not
  appear right away.
- **Codex**: run `codex mcp login sloe-data`, approve the browser connection,
  then start a new task.

Codex users can also connect without the plugin:

```bash
codex mcp add sloe-data --url https://mcp.sloedata.com/data-mcp
codex mcp login sloe-data
```

## Tools

All 15 tools are read-only.

- `sloedata_get_industry_summary`
- `sloedata_search_institutions`
- `sloedata_get_institution`
- `sloedata_get_history`
- `sloedata_compare_institutions`
- `sloedata_get_peers`
- `sloedata_get_branches`
- `sloedata_get_industry_trends`
- `sloedata_get_mergers`
- `sloedata_get_vendor_relationships`
- `sloedata_list_datasets`
- `sloedata_get_dataset`
- `sloedata_get_hmda`
- `sloedata_get_institution_profile`
- `sloedata_get_call_report`

## Data and privacy

The connector sends each tool call's arguments (for example a credit union name,
charter number, or dataset name) to the Sloe Data MCP server, which returns the
requested data. It does not send your conversation, and it does not store
requests or tokens. See the
[connector privacy policy](https://mcp.sloedata.com/connector-privacy.html)
and [documentation](https://mcp.sloedata.com/connector-docs.html).

## Boundaries

- Read-only. No admin or editorial tools.
- No public or free default key.
- No keys in prompts, URLs, docs, tickets, screenshots, or repositories.
- When the connector is unauthenticated or fails, the assistant reports the
  problem instead of answering from public NCUA data, the web, or memory.
- No bulk export, resale, redistribution, or warehouse sync through the
  connector.

## Troubleshooting

**"Connector is not authenticated"**: connect your account using the steps
above.

**"Invalid or revoked API key"**: the connected account no longer has an active
paid plan. Email info@thecreditunionwire.com.

**The skill loads but no `sloedata_*` tools exist**: the client is holding a
stale tool list. Reconnect, then start a new session or task.

## Support

Email info@thecreditunionwire.com.
