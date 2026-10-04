---
name: search-console-analysis
description: Answer questions about a site's Google Search traffic, rankings, indexing and sitemaps with the QuerySail Google Search Console tools. Use when the user asks about clicks, impressions, CTR, average position, top queries or pages, traffic drops, whether a page is indexed, canonicals, or sitemap errors.
---

# Search Console analysis with QuerySail

The QuerySail MCP server (`querysail-gsc`) gives read-only access to the user's own Google Search Console data.

## Workflow

1. Call `list_properties` first and use the exact `property_id` it returns (for example `sc-domain:example.com` or `https://www.example.com/`). Do not guess property IDs.
2. Pick the tool:
   - Totals, trends or breakdowns by query, page, country, device, date, hour or search appearance: `get_performance`.
   - "What changed", drops, gains, before/after: `compare_performance`.
   - "Is this page indexed", canonical, last crawl, noindex or robots blocking: `inspect_urls` (up to 20 URLs per call, all in the same property).
   - Sitemap status, errors or warnings: `list_sitemaps`.
3. Default to the last 28 days unless the user names a range.

## Reporting

- Always state the property, the exact date range and any filters used. Search Console dates are in Pacific Time and the latest 2–3 days can be incomplete.
- CTR is returned as a fraction; show it as a percentage.
- Query and page breakdowns contain only Google's top rows, so they may not add up to the totals. Say so when it matters.
- Report changes between periods as observed differences, not causes.
- URL inspection describes Google's last indexed version, not a live test.

## Limits

QuerySail is read-only. It cannot submit sitemaps, request indexing or change settings; tell the user to do that in Search Console. It has no Google Analytics data and no keyword search volume.
