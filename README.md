# QuerySail: Google Search Console MCP server

<img src="assets/logo.png" alt="QuerySail logo" width="96" align="right">

QuerySail is a hosted, read-only [MCP](https://modelcontextprotocol.io) server for Google Search Console. Connect it to ChatGPT, Claude, Codex, Cursor or VS Code, sign in, connect your Google account once, and ask about your search traffic in plain language. There is nothing to install and no API key to paste.

- **Endpoint:** `https://mcp.querysail.com/gsc/mcp` (Streamable HTTP, OAuth 2.1 with dynamic client registration)
- **Website:** https://querysail.com/google-search-console
- **Pricing:** 7-day free trial, no card required, then $8/month. See https://querysail.com/pricing.

This repository holds the public listing files (MCP Registry `server.json`, a Claude Code plugin, client configs). The service itself is hosted at querysail.com; its source is not published here.

## Tools

All tools are read-only. QuerySail cannot submit sitemaps, request indexing or change any Search Console setting.

| Tool | What it does |
|---|---|
| `list_properties` | Lists the Search Console properties your connected Google accounts can read, with exact IDs and permission level. |
| `get_performance` | Clicks, impressions, CTR and average position, as totals or by date, hour, query, page, country, device or search appearance, with filters. Covers web, image, video, news, Discover and Google News results. |
| `compare_performance` | Compares two periods (previous period, same weekdays a year earlier, or custom dates) and lists the queries, pages, countries or devices that gained or lost the most. |
| `inspect_urls` | Inspects up to 20 URLs: whether each is on Google, why not, last crawl, robots.txt and noindex blocking, and the canonical Google chose. |
| `list_sitemaps` | Lists submitted sitemaps with when Google last read them, URL counts, errors and warnings. |

## Example prompts

- Which of my pages got the most clicks from Google in the last 28 days?
- What changed in my search traffic compared with the previous 28 days?
- Is my homepage indexed on Google, and which canonical did Google choose?
- Which queries rank on page two for my blog, sorted by impressions?

## Setup

You need a QuerySail account with Google Search Console connected: sign up at https://querysail.com. Each client below opens a browser window to sign in the first time.

### Claude Code

```bash
claude mcp add --transport http --scope user querysail-gsc https://mcp.querysail.com/gsc/mcp
```

Then run `/mcp`, choose `querysail-gsc` and sign in. Or install it as a plugin, which also adds a Search Console analysis skill:

```text
/plugin marketplace add querysail/querysail-gsc
/plugin install querysail-gsc@querysail
```

### Claude Desktop and claude.ai

Open **Customize → Connectors**, select **+ Add → Add custom connector**, name it QuerySail and paste `https://mcp.querysail.com/gsc/mcp` as the server URL.

### ChatGPT

Turn on **Developer mode** under **Settings → Security and login**, go to [chatgpt.com/plugins](https://chatgpt.com/plugins), select the plus button and paste the endpoint as the connection URL.

### Codex

```bash
codex mcp add querysail-gsc --url https://mcp.querysail.com/gsc/mcp
codex mcp login querysail-gsc
```

### Cursor

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "querysail-gsc": { "url": "https://mcp.querysail.com/gsc/mcp" }
  }
}
```

### VS Code

Run **MCP: Add Server**, choose **HTTP** and paste the endpoint, or add to `mcp.json`:

```json
{
  "servers": {
    "querysail-gsc": { "type": "http", "url": "https://mcp.querysail.com/gsc/mcp" }
  }
}
```

### Clients that only run local commands

Use the [`mcp-remote`](https://www.npmjs.com/package/mcp-remote) bridge (needs Node.js):

```json
{
  "mcpServers": {
    "querysail-gsc": { "command": "npx", "args": ["mcp-remote", "https://mcp.querysail.com/gsc/mcp"] }
  }
}
```

## Data and limits

- Google access uses the `webmasters.readonly` scope. Refresh tokens are encrypted at rest. You can disconnect Google or delete your account at any time.
- Search Console keeps about 16 months of data; hourly data covers the last 10 days; Google returns only top rows for query and page breakdowns.
- Privacy: https://querysail.com/privacy · Terms: https://querysail.com/terms · Support: https://querysail.com/support

## License

The files in this repository are MIT licensed. The QuerySail service is subject to its [terms](https://querysail.com/terms).
