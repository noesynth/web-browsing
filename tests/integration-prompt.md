# Integration Test: web-browsing

Use this prompt in a Claude Code session with web-browsing skills to test both depth tiers against live MCP servers.

## Prerequisites

- brave-search MCP configured
- apify MCP configured (with APIFY_TOKEN)

## Test Topic

**"model context protocol MCP server development best practices 2025"**

This topic is ideal because:
- Well-covered on the web (official docs, blog posts, tutorials)
- Has authoritative primary sources (modelcontextprotocol.io, docs.anthropic.com)
- Active development area with recent content
- Tests both brave-search (discovery) and apify (full-page fetch)

---

## Test 1: web-search (shallow scan)

**Invoke:** Use the web-search skill

**Prompt:** "Search for MCP server development best practices in 2025. What resources exist?"

**Expected behavior:**
- Calls `brave_web_search` with relevant query
- Returns a list of URLs with titles and snippets
- Does NOT call `apify/rag-web-browser`
- Does NOT present snippet content as authoritative analysis
- Clearly labels results as orientation/discovery

**Failure conditions:**
- Calls apify/rag-web-browser (wrong skill tier)
- Presents snippet content as definitive answers
- Draws analytical conclusions from snippets alone
- Claims to understand page content from snippet only

---

## Test 2: web-research (deep research)

**Invoke:** Use the web-research skill

**Prompt:** "Research MCP server development best practices in 2025. Provide a comprehensive analysis of current patterns, recommended architectures, and common pitfalls based on official documentation and community resources."

**Expected behavior:**
- Calls `brave_web_search` to discover candidate URLs
- Selects 3-10 relevant URLs from results
- Calls `apify/rag-web-browser` for each selected URL (minimum 3)
- Bases analysis on full page content (quotes, paraphrases, specific details)
- Cross-references information across multiple fetched pages
- Cites source URLs for claims

**Failure conditions:**
- Completes research using only brave-search snippets
- Fetches fewer than 3 pages via apify
- Draws conclusions not traceable to full-page content
- Presents snippet summaries as research findings

---

## Test 3: HARD-GATE enforcement

**Verification checklist:**

For web-search:
- [ ] No apify calls made
- [ ] Results clearly labeled as snippets/orientation
- [ ] No substantive conclusions drawn from snippets

For web-research:
- [ ] apify/rag-web-browser called for every analyzed page
- [ ] Minimum 3 full pages fetched
- [ ] All conclusions cite full-page content
- [ ] No snippet-only claims in final output

## Success Criteria

1. web-search uses ONLY brave-search tools (no apify)
2. web-research fetches full content for every page analyzed (minimum 3)
3. HARD-GATEs respected — no depth-tier violations
4. Both skills produce useful, structured output appropriate to their depth
5. Failed page fetches (paywalls, etc.) are noted and alternatives tried
