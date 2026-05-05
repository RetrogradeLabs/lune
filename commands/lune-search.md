---
name: lune-search
description: Search Lune for papers
arguments:
  - name: query
    description: Search query
    required: true
---

Use the `search_papers` tool with the user's query: {{query}}.
Return the top 10 results in a compact table (rank, title, conf, year, score).
