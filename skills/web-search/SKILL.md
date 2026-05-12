---
name: Web Search
description: Quick web scanning — discover pages, get snippets, find URLs. For orientation only, not substantive analysis.
type: sop
layer: sop
tools:
  brave-search: [brave_web_search, brave_news_search, brave_video_search, brave_image_search, brave_local_search, brave_summarizer]
input: query (string)
output: SearchResult[] with title, URL, snippet, date
---

# Web Search SOP

## Layer Rules
- **Layer**: sop — wraps MCP tools directly
- **Called by**: Any tactic or strategy requiring quick web orientation
- **Calls**: brave-search MCP tools (never calls other SOPs)

## Purpose

Fast orientation search. Understand what pages and sources exist on a topic. Suitable for quick fact-checking, getting an overview of a landscape, or finding URLs for deeper research via web-research.

Use this when you need to:
- Find what pages exist on a topic
- Get a rough overview of available information
- Discover URLs for deeper research (via web-research)
- Quick fact-check with snippet-level confidence
- Monitor recent news on a topic

**This skill returns snippets only.** For full-page content analysis, use `web-research`.

## Tools

| Tool | Purpose | Returns |
|------|---------|---------|
| `brave_web_search` | General web search | URL, title, description snippet |
| `brave_news_search` | Recent news articles | URL, title, snippet, date |
| `brave_video_search` | Video content discovery | URL, title, description, duration |
| `brave_image_search` | Image search | URL, title, image properties |
| `brave_local_search` | Local businesses/places | Name, address, rating, hours |
| `brave_summarizer` | AI-generated summary (Pro only) | Summarized text with references |

## HARD-GATE

<HARD-GATE>
**brave-search returns snippets only (1-3 sentences per result).**

Snippets are NOT authoritative content. They are orientation signals.

**PROHIBITED:**
- Drawing substantive conclusions from snippets alone
- Treating snippet content as verified facts
- Completing a research task using only search results
- Presenting snippet summaries as thorough analysis

**REQUIRED:**
- Label all results as "snippets for orientation"
- For any analysis requiring full content, escalate to `web-research`
- Make clear to the user that snippets provide direction, not answers
</HARD-GATE>

## Workflow

### Step 1: Formulate Query

- Use specific, targeted keywords
- Consider `freshness` filter: `pd` (24h), `pw` (7d), `pm` (31d), `py` (365d)
- Consider `count` parameter: 5-20 results (default 10)
- For news: use `brave_news_search` instead of `brave_web_search`
- For location-based: use `brave_local_search`

### Step 2: Execute Search

**General web search:**
```
brave_web_search(query="your search terms", count=10)
```

**News search (time-sensitive topics):**
```
brave_news_search(query="topic", freshness="pw", count=10)
```

**Video search:**
```
brave_video_search(query="topic", count=10)
```

**Image search:**
```
brave_image_search(query="topic", count=20)
```

**Local search:**
```
brave_local_search(query="restaurants near me", count=5)
```

### Step 3: Return Structured Results

For each result, present:
- **Title** — page title
- **URL** — full URL (for reference or escalation to web-research)
- **Snippet** — 1-3 sentence description (orientation only, not authoritative)
- **Date** — when available (especially for news)

## Tool-Specific Notes

### brave_web_search
- `query` (required): search terms, max 400 chars
- `count`: 1-20 results (default 10)
- `offset`: pagination, 0-9 (default 0)
- `freshness`: time filter — `pd` (24h), `pw` (7d), `pm` (31d), `py` (365d), or date range `YYYY-MM-DDtoYYYY-MM-DD`
- `safesearch`: off / moderate / strict (default moderate)
- `search_lang`: language code (default en)
- `country`: 2-letter country code (default US)
- `result_filter`: array of types to include (web, news, videos, etc.)

### brave_news_search
- Same core parameters as web search
- Better for time-sensitive queries
- Returns publication dates
- Default freshness: `pd` (last 24 hours)

### brave_video_search
- Returns `duration` and `thumbnail_url` in addition to standard fields
- Good for tutorial/educational content discovery

### brave_image_search
- `count`: 1-200 results (default 50)
- Returns image properties (URL, dimensions)
- Good for visual content discovery

### brave_local_search
- Best for "near me" or location-specific queries
- Returns: business name, address, rating, review count, phone, hours
- Falls back to web search if no local results found

### brave_summarizer
- Requires Pro plan
- Must first run `brave_web_search` with `summary=true`
- Returns AI-generated summary with optional inline references

## Examples

**Quick fact check:**
```
brave_web_search(query="Claude 3.5 Sonnet release date", count=5)
```

**News monitoring:**
```
brave_news_search(query="transformer architecture breakthroughs", freshness="pm", count=10)
```

**Landscape scan:**
```
brave_web_search(query="MCP server frameworks comparison 2025", count=15)
```

**Finding documentation:**
```
brave_web_search(query="anthropic claude API tool use documentation", count=5)
```

**Video tutorials:**
```
brave_video_search(query="how to build MCP server tutorial", count=10)
```
