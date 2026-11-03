---
name: querysail-search-console
description: Answer questions about a site's Google Search traffic, rankings, indexing and sitemaps with the QuerySail Google Search Console tools. Use when the user asks about clicks, impressions, CTR, average position, top queries or pages, traffic drops, whether a page is indexed, canonicals, sitemap errors, or SEO quick wins such as striking-distance keywords, low-CTR pages, keyword cannibalization and rising or falling queries.
---

# Search Console analysis with QuerySail

The QuerySail MCP server (`querysail-gsc`) gives read-only access to the user's own Google Search Console data.

## Setup when the tools are missing

If no `querysail-gsc` tools (such as `list_properties`) are available, the server is not connected. Ask the user before you change their configuration, then add it for their client:

- Claude Code: run `claude mcp add --transport http --scope user querysail-gsc https://mcp.querysail.com/gsc/mcp`. The user then runs `/mcp`, chooses `querysail-gsc` and signs in.
- Codex: run `codex mcp add querysail-gsc --url https://mcp.querysail.com/gsc/mcp`, then `codex mcp login querysail-gsc`, which opens the sign-in page.
- Cursor: add `"querysail-gsc": { "url": "https://mcp.querysail.com/gsc/mcp" }` under `mcpServers` in `~/.cursor/mcp.json`. The user then connects it in Cursor's MCP settings and signs in.
- VS Code: add `"querysail-gsc": { "type": "http", "url": "https://mcp.querysail.com/gsc/mcp" }` under `servers` in `mcp.json`. The user signs in when VS Code asks.
- Other clients: follow https://querysail.com/google-search-console#connect.

The user signs in with a QuerySail account that has Google Search Console connected. If they have none, they can start the free trial at https://querysail.com. There is no API key. The tools may only appear in a new session.

## Workflow

1. Call `list_properties` first and use the exact `property_id` it returns (for example `sc-domain:example.com` or `https://www.example.com/`). Do not guess property IDs.
2. Pick the tool:
   - Totals, trends or breakdowns by query, page, country, device, date, hour or search appearance: `get_performance`.
   - "What changed", drops, gains, before/after: `compare_performance`.
   - "Is this page indexed", canonical, last crawl, noindex or robots blocking: `inspect_urls` (up to 20 URLs per call, all in the same property).
   - Sitemap status, errors or warnings: `list_sitemaps`.
   - Quick wins, opportunities or "what should I work on": follow the recipes under Quick wins.
3. Default to the last 28 days unless the user names a range.

## Quick wins

Use these recipes when the user asks for SEO opportunities. The thresholds are starting points. Loosen them for a small site and tighten them for a large one. Exclude brand queries with an `excludingRegex` filter on `query` (for example `(?i)brandname`), because they behave differently. Rows come back sorted by clicks, so page through 2 or 3 pages of 100 rows to reach queries with few clicks.

### Striking-distance queries

Queries that rank just off the top results, where a small gain in position can add many clicks.

1. Call `get_performance` with `dimensions: ["query", "page"]` and `row_limit: 100`.
2. Keep rows with an average position from 8 to 20 and at least 100 impressions.
3. Rank them by impressions. Show the query, the page that ranks for it, the position and the impressions.

### Low-CTR pages

Pages that rank well but get fewer clicks than other pages at a similar position.

1. Call `get_performance` with `dimensions: ["page"]` and `row_limit: 100`. Use `["query"]` for the same check on queries.
2. Keep rows with an average position of 5 or better and at least 500 impressions.
3. Compare each row's CTR with the median CTR of the other rows at a similar position on the same site. Do not use industry CTR benchmarks.
4. Flag rows at less than half that median. The title and meta description are worth checking, but say that the data does not show the cause. Rich results and ads above the result can also lower CTR.

### Keyword cannibalization

One query where two or more pages from the same site split the impressions.

1. Call `get_performance` with `dimensions: ["query", "page"]` and `row_limit: 100`.
2. Group the rows by query. Flag queries where two or more pages each get at least 20% of the query's impressions.
3. To confirm a case, call `get_performance` with a `query` `equals` filter and `dimensions: ["date", "page"]`. Pages that swap places over time are a stronger sign than two pages that rank side by side.
4. URL variants (http and https, www and non-www, trailing slashes) of one page are not cannibalization. Report them as a canonical issue and check them with `inspect_urls`.

### Rising and falling

1. Call `compare_performance` with `dimension: "query"` or `"page"`. The default compares the last 28 days with the 28 days before.
2. Use `rank_by: "impressions"` to see early changes before clicks move. Use `compare_to: "previous_year"` for seasonal sites.
3. Report the gainers and losers as observed changes. For a loser, check the page with `inspect_urls` before you suggest any other reason.

## Reporting

- Always state the property, the exact date range and any filters used. Search Console dates are in Pacific Time and the latest 2–3 days can be incomplete.
- CTR is returned as a fraction; show it as a percentage.
- Query and page breakdowns contain only Google's top rows, so they may not add up to the totals. Say so when it matters.
- Report changes between periods as observed differences, not causes.
- URL inspection describes Google's last indexed version, not a live test.

## Limits

QuerySail is read-only. It cannot submit sitemaps, request indexing or change settings; tell the user to do that in Search Console. It has no keyword search volume. Google Analytics data comes from the separate `querysail-ga4` server.
