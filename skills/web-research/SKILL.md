---
name: Web Research
description: Deep web research — fetches full page content for analysis. Snippets alone are PROHIBITED for conclusions.
type: sop
layer: sop
tools:
  brave-search: [brave_web_search, brave_news_search]
  apify: [rag-web-browser]
input: query (string), depth (standard | thorough)
output: PageAnalysis[] with full markdown content + source URL
---

# Web Research SOP

## Layer Rules
- **Layer**: sop — wraps MCP tools directly
- **Called by**: Any tactic or strategy requiring deep web content analysis
- **Calls**: brave-search MCP tools, apify MCP tools (never calls other SOPs)

## Purpose

Deep web research. Fetch full page content for selected URLs and analyze thoroughly. Suitable for specific topic investigation, competitive analysis, technical documentation reading, and any task requiring substantive understanding of web content.

Use this when you need to:
- Understand a topic thoroughly from web sources
- Read full documentation or articles
- Compare information across multiple sources
- Draw substantive conclusions backed by full content
- Investigate technical details from official docs

**This skill REQUIRES full-page content fetching.** Snippet-only analysis is prohibited.

## Tools

| Tool | Role | Returns |
|------|------|---------|
| `brave_web_search` | Discovery — find candidate URLs | URL, title, snippet |
| `brave_news_search` | Discovery — recent news URLs | URL, title, snippet, date |
| `apify/rag-web-browser` | Full-page fetch — get complete content | Full page as markdown |

### Tool Roles
- **brave-search** = discovery only (find URLs worth reading)
- **apify/rag-web-browser** = content fetching (get the actual page content)

## HARD-GATE

<HARD-GATE>
**brave-search snippets are NOT sufficient for research.**

For EVERY page selected for analysis, you MUST fetch full content via `apify/rag-web-browser`.

**PROHIBITED:**
- Completing a research task using only snippets
- Drawing conclusions without reading full page content
- Presenting snippet summaries as research findings
- Citing information not verified in full-page content

**REQUIRED:**
- Call `apify/rag-web-browser` for every page you analyze
- Minimum 3 pages fetched via apify for any research task
- Every analytical claim must be traceable to full-page content
- Cross-reference claims across multiple fetched pages
</HARD-GATE>

## Workflow

### Step 1: Discover

Find candidate URLs via brave-search:

```
brave_web_search(query="your research topic", count=10)
```

For time-sensitive topics:
```
brave_news_search(query="topic", freshness="pw", count=10)
```

Use snippets ONLY to assess relevance for URL selection — not for analysis.

### Step 2: Select

Choose 3-10 most relevant URLs based on:
- Title relevance to research question
- Snippet suggests substantive content (not just a landing page)
- Source authority (official docs, reputable publications, expert blogs)
- Recency (prefer recent for fast-moving topics)
- Diversity of perspectives (avoid single-source bias)

### Step 3: Fetch

For each selected URL, fetch full content:

```
apify/rag-web-browser(query="<URL>", maxResults=1, outputFormats=["markdown"])
```

**Parameters:**
- `query` (required): the URL to fetch (when fetching a specific page) or search terms
- `maxResults` (default 3): set to 1 when fetching a known URL, 3-5 when searching
- `outputFormats` (default ["markdown"]): always use `["markdown"]` for LLM consumption

**When fetching a known URL:** pass the full URL as `query`, set `maxResults=1`
**When doing broad discovery:** pass search terms as `query`, set `maxResults=3`

### Step 4: Analyze

Base ALL conclusions on full page content:
- Quote or paraphrase specific passages
- Cross-reference claims across multiple fetched pages
- Note contradictions between sources
- Identify gaps in coverage
- Cite the source URL for each claim

### Step 5: Follow (Optional)

If fetched pages contain links to deeper resources:
- Extract relevant URLs from the markdown content
- Repeat Steps 3-4 for high-value links
- Build a comprehensive picture from multiple layers
- Useful for: documentation trails, reference chains, related articles

## Tool-Specific Notes

### apify/rag-web-browser
- `query` (required): Google Search keywords OR a specific URL to fetch
- `maxResults` (default 3): number of pages to scrape
  - Use `1` when fetching a known URL
  - Use `3-5` when doing a broad search via query terms
- `outputFormats` (default ["markdown"]): always use `["markdown"]`
- Some pages may fail to fetch (paywalls, JS-heavy SPAs, anti-bot) — note failures and try alternatives
- Rate limiting: sequential fetches are fine, no special throttling needed
- Large pages may be truncated — check if content seems incomplete

### brave_web_search (discovery only)
- Use ONLY for URL discovery, NOT for content analysis
- `count=10` is usually sufficient for finding good candidates
- Combine with `freshness` filter for time-sensitive research
- Snippets are for relevance assessment only

### brave_news_search (discovery only)
- Better than web search for recent events
- Returns publication dates — useful for recency filtering
- Same parameters as brave_web_search

## Examples

### Technical documentation research: "MCP server development"

```
# Step 1: Discover
brave_web_search(query="model context protocol MCP server development guide 2025", count=10)

# Step 2: Select top 5 URLs (official docs, tutorials, blog posts)

# Step 3: Fetch each
apify/rag-web-browser(query="https://modelcontextprotocol.io/docs/concepts/servers", maxResults=1, outputFormats=["markdown"])
apify/rag-web-browser(query="https://docs.anthropic.com/en/docs/build-with-claude/mcp", maxResults=1, outputFormats=["markdown"])
# ... repeat for all selected URLs

# Step 4: Analyze full content, cross-reference, synthesize
```

### Competitive analysis: "AI code assistants"

```
# Step 1: Discover
brave_web_search(query="AI code assistant comparison review 2025", count=15)

# Step 2: Select 5-8 comparison articles and official product pages

# Step 3: Fetch
apify/rag-web-browser(query="https://example.com/ai-code-assistant-comparison", maxResults=1, outputFormats=["markdown"])
# ... repeat

# Step 4: Compare features, pricing, capabilities across sources
```

### News deep-dive: "recent AI announcements"

```
# Step 1: Discover via news
brave_news_search(query="GPT-5 announcement details capabilities", freshness="pw", count=10)

# Step 2: Select 3-5 most detailed articles

# Step 3: Fetch full articles
apify/rag-web-browser(query="https://news-site.com/gpt5-full-article", maxResults=1, outputFormats=["markdown"])
# ... repeat

# Step 4: Synthesize details from multiple full articles
```
