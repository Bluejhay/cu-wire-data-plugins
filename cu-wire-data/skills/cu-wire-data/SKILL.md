---
name: cu-wire-data
description: Use CU Wire Data through the hosted read-only MCP server for licensed U.S. credit union data. Trigger when the user asks for CU Wire Data, credit union industry summaries, credit union institution search, NCUA charter lookups, HMDA mortgage lending, CUSO networks, M&A, regulatory data, or other research through the CU Wire Data product.
---

# CU Wire Data

Use the bundled `cu-wire-data` MCP server for licensed CU Wire Data requests.

## Authentication

The plugin uses browser-based OAuth account connect through CU Wire Data. The
assistant opens a CU Wire Data sign-in and approval page, and the client returns
holding a scoped read-only connector token.

Never print or paste API keys, OAuth codes, connector tokens, or bearer tokens
into chat, files, PRs, command output, screenshots, or docs. If the OAuth flow
fails, treat that as a setup failure and ask the user to reconnect through their
client's MCP connect flow.

Do not tell plugin users to set `CUWIREDATA_API_KEY` for normal usage. That is a
legacy/manual MCP setup path, not the public plugin path. For the public plugin,
the correct fix is an OAuth reconnect plus a fresh session or task.

## Failure Handling

If a CU Wire Data tool call fails because the connector is unauthenticated,
unlicensed, expired, missing a bearer token, missing an API key, or otherwise
unable to return licensed CU Wire Data, stop and report the connection problem.
Do not answer the CU Wire Data request from public NCUA data, web search, cached
data, model memory, prior runs, screenshots, PDFs, or any other fallback source.

Do not write a "using public NCUA fallback instead" response. That is a product
failure because it disguises a licensed-connector setup problem as a data
answer.

If the CU Wire Data skill is available but the `cuwiredata_*` tools are not
exposed, report this as a plugin tool-discovery or session-refresh problem, and
give the user the reconnect steps **for the client they are actually running**.
Do not claim the product requires a local API key in that case.

- **Claude Code**: run `/mcp`, select `cu-wire-data`, choose Authenticate, and
  approve read-only access in the browser. Then start a new session if the tools
  still do not appear.
- **Codex**: run `codex mcp login cu-wire-data`, approve the browser connection,
  then start a new task or restart the desktop app.
- **Any other MCP client**: reconnect the `cu-wire-data` server through that
  client's own MCP authorization flow, then start a fresh session.

If you cannot tell which client you are running in, describe the reconnect step
generically ("reconnect the CU Wire Data connector through your assistant's MCP
connect flow") rather than naming the wrong client's command.

## Tools

The hosted MCP server exposes read-only tools:

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

## Usage Rules

- Treat the data as licensed product data.
- Public plugin installation does not grant data access; successful calls require a licensed CU Wire Data account.
- Failed licensed tool calls must not be substituted with public data.
- Preserve period, source, and as-of context returned by tools.
- Do not claim realtime coverage beyond the returned period.
- Do not present model-written summaries as source material.
- Do not provide legal, investment, regulatory, or safety-and-soundness advice from connector results.
- Do not help users bulk export, scrape, warehouse-sync, resell, or redistribute licensed data through this connector.
- Keep result sets small and interactive unless the user's license explicitly covers higher-volume API use.
- Do not use CU Wire editorial/admin MCP routes for Data Access requests.
- For direct API examples, use `charter=5536`, not `charter_number=5536`.

## Safe Answer Shape

When summarizing tool output:

1. State the returned period and source.
2. Explain the filters or charter used.
3. Summarize only fields present in the returned data.
4. Mention when a question requires a different license, endpoint, or human review.

## Good Starter Requests

- Get the current U.S. credit union industry summary.
- Find the largest credit unions in North Carolina.
- Look up Navy Federal by NCUA charter 5536.


## Full research access

Start with `cuwiredata_list_datasets` for a requested family beyond core financial summaries. Use `cuwiredata_get_dataset` with a returned ID and follow its section pointers to nested data. Follow `next_offset` with the same `dataset_version` until all records needed for the answer have been retrieved; never describe a partial page as a complete population. Restart if the dataset changes. This includes HMDA, CUSOs, CU acquisitions of banks, regulations, enforcement, FedNow, CDFI, leadership, fees, Canadian institutions, rankings and geographic markets.

`cuwiredata_get_hmda` defaults to the CU filer list. Use `where: {"n":"13"}` for a charter, or `where: {"yr":2025}` for a filing year. Preserve annual history and the actual `yr` on each filer. The dataset year is not every filer's year. `hel` means home improvement, not HELOC; CUSO data reflects filings under the CUSO's own LEI.

Use `cuwiredata_get_institution_profile` for all approved financial profile fields, and `cuwiredata_get_call_report` for original reported field strings. Keep these release IDs distinct from independently refreshed HMDA and other research datasets.

A tool-list refresh or fresh Codex chat may be needed after the server expands. If existing authenticated calls succeed, do not create another key or request another login solely to discover the added tools.
