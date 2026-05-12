# web-browsing

Claude Code skills for rigorous web research — enforces full-page content reading, prevents shallow snippet-only analysis.

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
| `brave-search` | Discovery | URL, title, snippet (NOT full content) |
| `apify/rag-web-browser` | Full-page fetch | Complete page as markdown |

## MCP Server Setup

### brave-search

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

### apify

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

## Usage

### web-search (quick scan)

Use when you need to:
- Find what pages exist on a topic
- Get a rough overview of available information
- Discover URLs for deeper research
- Quick fact-check with snippet-level confidence

### web-research (deep analysis)

Use when you need to:
- Understand a topic thoroughly from web sources
- Read full documentation or articles
- Compare information across multiple sources
- Draw substantive conclusions backed by full content

## Project Structure

```
web-browsing/
├── skills/
│   ├── web-search/
│   │   └── SKILL.md        # Shallow — snippet-based orientation
│   └── web-research/
│       └── SKILL.md        # Deep — full-page content analysis
├── tests/
│   └── integration-prompt.md
├── assets/
│   └── repo-info.txt
├── README.md
├── .gitignore
└── LICENSE
```

## Version

**v1.0.0** — Two depth-tiered skills (web-search, web-research)

## License

[Apache-2.0](LICENSE)
