# Lune — Claude Code plugin

Top-tier paper search, citation graphs, conference monitoring, and curated
research best-practices — for your Claude Code agent.

This plugin bundles:

- **MCP server** (`mcp.luneresearch.com`) — `search_papers`, `get_paper`,
  `get_paper_fulltext`, `get_paper_citations`, `get_conferences`,
  `get_conference_papers`, `subscribe_conference`,
  `search_research_guidance`, etc.
- **Four research skills** (`skills/`) — paper search, literature review,
  conference monitoring, research guidance. Claude auto-invokes them when the
  description matches the user's request.
- **Three slash commands** (`commands/`) — `/lune-search`, `/lune-cite`,
  `/lune-watch`.

## Install

```bash
# Inside Claude Code
/plugin marketplace add RetrogradeLabs/lune
/plugin install lune@retrograde-labs-lune
```

Then set your Lune API key (free tier available, sign up at
<https://luneresearch.com>):

```bash
export LUNE_API_KEY=lune_xxx
```

The MCP server reads the key from `${LUNE_API_KEY}` at request time. Restart
Claude Code after the first install so it picks up the env var.

## Skills

| Skill | When Claude invokes it |
|---|---|
| `lune-paper-search` | "Find me papers on X", "what's been published on Y" |
| `lune-literature-review` | "Write a literature review of …", "summarise the state of …" |
| `lune-conference-monitor` | "Watch CCS 2025 for new papers", "subscribe me to …" |
| `lune-research-guidance` | "How should I structure a paper review?", "what's the SOP for …" |

## Slash commands

```
/lune-search <query>             # Search Lune for papers
/lune-cite <paper_id>            # Pull citations for a paper, sorted by inbound count
/lune-watch <conference>         # Subscribe + drain new papers
```

## Source

- Plugin: <https://github.com/RetrogradeLabs/lune>
- MCP server: <https://github.com/luneresearch/lune-mono/tree/main/apps/mcp>
- Lune platform: <https://luneresearch.com>

## License

MIT
