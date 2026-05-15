# web-browsing

**Claude Code skills for rigorous web research — enforces full-page content reading, prevents shallow snippet-only analysis.**

- 🔍 **Quick discovery** — find relevant pages, get snippets, discover URLs for any topic
- 📄 **Full-page reading** — fetch complete page content as markdown for thorough analysis
- 🚫 **HARD-GATE enforcement** — prevents treating search snippets as sufficient evidence
- 🎯 **Depth-tiered skills** — choose the right level of rigor for your task

## What is this?

This is a set of [Claude Code skills](https://docs.anthropic.com/en/docs/claude-code) that enforce rigorous web research methodology. It provides two depth-tiered skills — one for quick snippet-based scanning, one for full-page content research — ensuring AI assistants never treat search snippets as authoritative evidence.

Designed for Claude Code sessions where web research quality matters. Uses [Brave Search](https://brave.com/search/api/) for discovery and [Apify RAG Web Browser](https://apify.com/apify/rag-web-browser) for full-page content extraction.

## Skill Tiers

| Skill | Depth | When to Use |
|-------|-------|-------------|
| `web-search` | Shallow | Quick scan — find pages, get snippets, discover URLs |
| `web-research` | Deep | Full-page content fetching and thorough analysis |

## How It Works

Each skill enforces a HARD-GATE appropriate to its depth:

| Tier | HARD-GATE Rule |
|------|---------------|
| Search | Snippets are for orientation ONLY — no substantive conclusions |
| Research | MUST fetch full page content — snippets alone are PROHIBITED |

This prevents the common failure mode where an AI assistant treats search snippets as sufficient evidence for substantive claims.

## Tool Responsibility Matrix

| Tool | Role | Output |
|------|------|--------|
| `brave-search` | Discovery — web, news, video search | URL, title, snippet (NOT full content) |
| `apify/rag-web-browser` | Full-page content fetching | Complete page as markdown |

## Quick Start

### 1. Configure MCP Servers

Add both servers to your MCP configuration (`.mcp.json` or Claude Desktop config):

**brave-search**

```json
{
  "mcpServers": {
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/brave-search-mcp"],
      "env": {
        "BRAVE_API_KEY": "<your-key>"
      }
    }
  }
}
```

Get an API key at: https://brave.com/search/api/

**apify**

```json
{
  "mcpServers": {
    "apify": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/apify-mcp"],
      "env": {
        "APIFY_TOKEN": "<your-token>"
      }
    }
  }
}
```

Get a token at: https://console.apify.com/account/integrations

### 2. Install Skills

Clone this repository and add the skills directory to your Claude Code project:

```bash
git clone https://github.com/yogsoth-ai/web-browsing.git
```

### 3. Use in Claude Code

Invoke the skills by name in your Claude Code session:

- `/web-search` — quick snippet-based discovery
- `/web-research` — full-page content analysis

## Available Tools

### brave-search (Discovery)

| Tool | Description |
|------|-------------|
| `brave_web_search` | Search the web — returns ranked results with title, URL, and snippet |
| `brave_news_search` | Search recent news articles by topic |
| `brave_video_search` | Search for video content |

### apify/rag-web-browser (Full-Page Fetch)

| Tool | Description |
|------|-------------|
| `rag-web-browser` | Fetch any URL and return full page content as markdown |

## Usage

### web-search (quick scan)

Use when you need to:
- Quickly assess what information exists on a topic
- Find relevant URLs for deeper research
- Get a rough overview from snippets
- Discover sources before committing to full reads

### web-research (deep analysis)

Use when you need to:
- Understand a topic thoroughly from web sources
- Read full documentation, articles, or technical pages
- Compare information across multiple sources
- Draw substantive conclusions backed by full content
- Conduct competitive analysis or market research

## Project Structure

```
web-browsing/
├── skills/
│   ├── web-search/
│   │   └── SKILL.md          # Shallow — snippet-based orientation
│   └── web-research/
│       └── SKILL.md          # Deep — full-page content analysis
├── tests/
│   └── integration-prompt.md # Live integration test prompt
├── assets/
│   └── repo-info.txt
├── README.md
├── .gitignore
└── LICENSE
```

## Links

- 🐙 [GitHub repository](https://github.com/yogsoth-ai/web-browsing)
- 🔍 [Brave Search API](https://brave.com/search/api/)
- 🌐 [Apify RAG Web Browser](https://apify.com/apify/rag-web-browser)
- 🔧 [Model Context Protocol](https://modelcontextprotocol.io)

## Version

**v1.0.0** — Two depth-tiered skills (web-search, web-research)

## License

[Apache-2.0](LICENSE)
