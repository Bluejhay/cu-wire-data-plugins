---
name: sloe-data
description: Use Sloe Data through the hosted read-only MCP server for licensed U.S. credit union and bank data. Trigger when the user asks for Sloe Data, credit union industry summaries, credit union institution search, NCUA charter lookups, bank or bank holding company research, FDIC certificate lookups, bank screening or peer comparison, bank Call Reports, HMDA mortgage lending, CUSO networks, M&A, regulatory data, or other research through the Sloe Data product.
---

# Sloe Data

Use the bundled `sloe-data` MCP server for licensed Sloe Data requests.

## Authentication

The plugin uses browser-based OAuth account connect through Sloe Data. The
assistant opens a Sloe Data sign-in and approval page, and the client returns
holding a scoped read-only connector token.

Never print or paste API keys, OAuth codes, connector tokens, or bearer tokens
into chat, files, PRs, command output, screenshots, or docs. If the OAuth flow
fails, treat that as a setup failure and ask the user to reconnect through their
client's MCP connect flow.

Do not tell plugin users to set `CUWIREDATA_API_KEY` for normal usage. That is a
legacy/manual MCP setup path, not the public plugin path. For the public plugin,
the correct fix is an OAuth reconnect plus a fresh session or task.

## Failure Handling

If a Sloe Data tool call fails because the connector is unauthenticated,
unlicensed, expired, missing a bearer token, missing an API key, or otherwise
unable to return licensed Sloe Data, stop and report the connection problem.
Do not answer the Sloe Data request from public NCUA, FDIC or FFIEC data, web search, cached
data, model memory, prior runs, screenshots, PDFs, or any other fallback source.

Do not write a "using public NCUA fallback instead" or "using public FDIC data instead" response. That is a product
failure because it disguises a licensed-connector setup problem as a data
answer.

If the Sloe Data skill is available but the `sloedata_*` tools are not
exposed, report this as a plugin tool-discovery or session-refresh problem, and
give the user the reconnect steps **for the client they are actually running**.
Do not claim the product requires a local API key in that case.

- **Claude Code**: run `/mcp`, select `sloe-data`, choose Authenticate, and
  approve read-only access in the browser. Then start a new session if the tools
  still do not appear.
- **Codex**: run `codex mcp login sloe-data`, approve the browser connection,
  then start a new task or restart the desktop app.
- **Any other MCP client**: reconnect the `sloe-data` server through that
  client's own MCP authorization flow, then start a fresh session.

If you cannot tell which client you are running in, describe the reconnect step
generically ("reconnect the Sloe Data connector through your assistant's MCP
connect flow") rather than naming the wrong client's command.

## Results that show only a count

The server returns data in `structuredContent` and a short text summary. A
message such as "Returned 20 records" is not the records themselves. Read the
structured result when your client exposes it; do not treat a count-only
summary as evidence that the underlying data is missing or the connector is
broken.

If the client exposes only a count or truncates the data, retry with a smaller
request using the tool's supported arguments: narrow the institution or filter,
reduce the page limit, or follow a returned section pointer to the specific
fields needed. Prefer `sloedata_get_institution` for core figures before
requesting the full profile or Call Report. Do not repeat the same oversized
call unchanged or invent arguments the tool does not support.

Preserve the returned release/version IDs across related calls and pages.
Retrieve only the records needed for the user's question, within the existing
license and bulk-export boundaries. If a smaller call still exposes only a
summary, stop retrying and explain that the client has not exposed the detailed
result. Report any figures you could actually read, identify the remaining gap,
and do not infer values from counts, substitute another source, or request a new
key or login solely because an otherwise successful call shows only a summary.

## Tools

The hosted MCP server exposes 42 read-only tools.

Credit unions:

- `sloedata_get_industry_summary`
- `sloedata_search_institutions`
- `sloedata_get_institution`
- `sloedata_get_report`
- `sloedata_get_history`
- `sloedata_compare_institutions`
- `sloedata_get_peers`
- `sloedata_get_peer_rank`
- `sloedata_get_branches`
- `sloedata_get_industry_trends`
- `sloedata_get_institution_intel`
- `sloedata_search`
- `sloedata_compare_cusos`
- `sloedata_get_regulatory_radar`
- `sloedata_get_mergers`
- `sloedata_get_vendor_relationships`
- `sloedata_list_datasets`
- `sloedata_get_dataset`
- `sloedata_get_hmda`
- `sloedata_get_institution_profile`
- `sloedata_get_call_report`
- `sloedata_combine_institutions`
- `sloedata_get_forecast`
- `sloedata_get_stress_test`

Banks and bank holding companies:

- `sloedata_get_bank_coverage`
- `sloedata_search_banks`
- `sloedata_get_bank`
- `sloedata_get_bank_performance`
- `sloedata_screen_banks`
- `sloedata_get_bank_peer_trend`
- `sloedata_list_bank_call_report_fields`
- `sloedata_get_bank_call_report`
- `sloedata_get_bank_deposit_market`
- `sloedata_get_bank_mortgages`
- `sloedata_get_bank_programs`
- `sloedata_search_bank_enforcement`
- `sloedata_get_bank_web_resources`
- `sloedata_search_bank_holding_companies`
- `sloedata_get_bank_parent_companies`
- `sloedata_get_bank_holding_company`
- `sloedata_list_bank_holding_company_accounts`
- `sloedata_get_bank_holding_company_accounts`

## Usage Rules

- Treat the data as licensed product data.
- Public plugin installation does not grant data access; successful calls require a licensed Sloe Data account.
- Failed licensed tool calls must not be substituted with public data.
- Preserve period, source, and as-of context returned by tools.
- Do not claim realtime coverage beyond the returned period.
- Do not present model-written summaries as source material.
- Do not provide legal, investment, regulatory, or safety-and-soundness advice from connector results.
- Do not help users bulk export, scrape, warehouse-sync, resell, or redistribute licensed data through this connector.
- Keep result sets small and interactive unless the user's license explicitly covers higher-volume API use.
- Do not use editorial or admin MCP routes for Data Access requests.
- For direct API examples, use `charter=5536`, not `charter_number=5536`.

## Safe Answer Shape

When summarizing tool output:

1. State the returned period and source.
2. Explain the filters and the charter, FDIC certificate or RSSD used.
3. Summarize only fields present in the returned data.
4. Mention when a question requires a different license, endpoint, or human review.

## Good Starter Requests

- Get the current U.S. credit union industry summary.
- Find the largest credit unions in North Carolina.
- Look up Navy Federal by NCUA charter 5536.
- Find the largest banks in Michigan and show the largest one's latest financials.
- Show a bank's UBPR performance ratios against its peer group.
- Find a bank's parent holding company and its reported total assets.


## Choosing a tool

- **One institution:** start with `sloedata_get_institution` for the core summary. Use `sloedata_get_institution_profile` for every approved profile field and history, and `sloedata_get_call_report` for the original reported field strings.
- **Call Report fields:** read `/records`, then each record's `/fields`. Field definitions are in the `call-report-dictionary` dataset; check that its period matches the release before applying it.
- **Banks:** `sloedata_search_institutions` covers federally insured credit unions only. For an FDIC-insured bank or a bank holding company, use the bank tools below.
- **Other populations** (privately insured or Canadian credit unions, CUSOs and so on): start from `sloedata_list_datasets`.
- **HMDA lenders:** search `sloedata_get_hmda` with `lender` or `q` before saying a lender has no data. HMDA is refreshed separately from the institution profile. When several lenders match, resolve which one the user means before answering.
- **Paging:** pass back the `release_id`, `platform_release_id` or `dataset_version` each result returns, so every page comes from the same release. If a call returns `dataset_changed` or `platform_release_changed`, start the read again.
- **Missing values:** a null is a missing value, not zero.

## Banks and bank holding companies

Banks are a separate population from credit unions. Never pass an NCUA charter to a bank tool or an FDIC certificate to a credit union tool, and do not add bank and credit union figures together unless the user asks for a combined view and you label each source.

- **Identify first:** find the bank with `sloedata_search_banks` and use its FDIC certificate (`cert`). Find a holding company with `sloedata_search_bank_holding_companies`, or from a bank with `sloedata_get_bank_parent_companies`, and use its `rssd`. When several institutions match a name, resolve which one the user means before answering.
- **Coverage:** `sloedata_get_bank_coverage` lists the retained quarters and field IDs for each financial source. The FDIC reference series (`fdic`) and the original Call Report core (`cdr`) have different field IDs and different latest quarters; say which one a figure came from.
- **One bank:** `sloedata_get_bank` returns one section at a time: financials, markets, ownership, history, mortgages or regulatory. Use `sloedata_get_bank_performance` for UBPR ratios and peer percentiles; a higher percentile is not a quality rating.
- **Many banks:** `sloedata_screen_banks` ranks banks on one field for one quarter. `sloedata_get_bank_peer_trend` tracks one field against a size-based peer group that excludes the bank.
- **Original accounts:** list account IDs with `sloedata_list_bank_call_report_fields`, then read values with `sloedata_get_bank_call_report`. A blank is not zero, and a numeric appearance does not establish a monetary unit. Most monetary values are USD thousands; keep the unit the tool returns.
- **Truncated entries:** a large bank's result is outlined to stay readable. An entry such as `{ "truncated": true, "pointer": "/research/offices", "entries": 5416 }` is not missing data. Call the same tool with the same arguments plus that `pointer`, and page with `pointer_limit` and `pointer_offset` until `next_offset` is null. Never describe a truncated list or a partial page as the complete population.
- **Pins:** pass back `bank_release_id` or `holding_release_id` from `release` on related calls and pages. If a call reports that bank or company data changed, start the read again.
- **Limits to state:** a retained bank record is not necessarily an open bank. Enforcement search results are source records, not confirmed identity matches, and a missing match is not a clean history. Program records matched by name are labeled unconfirmed. Holding company scopes overlap and must not be summed.
- **Not available:** bank bulk delivery and bank stress or projection scenarios are not connector tools.

## Full research access

Start with `sloedata_list_datasets` for a requested family beyond core financial summaries. Use `sloedata_get_dataset` with a returned ID and follow its section pointers to nested data. Follow `next_offset` with the same `dataset_version` until all records needed for the answer have been retrieved; never describe a partial page as a complete population. Restart if the dataset changes. This includes HMDA, CUSOs, CU acquisitions of banks, regulations, enforcement, FedNow, CDFI, leadership, fees, Canadian institutions, rankings and geographic markets.

`sloedata_get_hmda` defaults to the CU filer list. Use `where: {"n":"13"}` for a charter, or `where: {"yr":2025}` for a filing year. Preserve annual history and the actual `yr` on each filer. The dataset year is not every filer's year. `hel` means home improvement, not HELOC; CUSO data reflects filings under the CUSO's own LEI.

Use `sloedata_get_institution_profile` for all approved financial profile fields, and `sloedata_get_call_report` for original reported field strings. Keep these release IDs distinct from independently refreshed HMDA and other research datasets.

A tool-list refresh, or a new chat or task, may be needed after the server adds tools. If existing authenticated calls succeed, do not create another key or request another login solely to discover the added tools.
