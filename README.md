# Lune Research

Research tools your coding agent can cite. Lune gives it full text from top-tier
venues (NeurIPS, ICLR, ACL, CVPR, USENIX Security, IEEE S&P and more), the citation
trails between those papers, and a curated library of methodology guidance.

This plugin bundles the Lune MCP server together with three workflow skills, so an
agent knows not just which tools exist but how to compose them into a literature
review, a draft fact-check, or grounded methodology advice.

## Install

You need a Lune account. Sign-in happens in the browser, so there is no API key
to copy anywhere. Installing does not sign you in on its own: run the sign-in
step below once, or your app will prompt you the first time it uses a Lune tool.

**Claude Code**

```
/plugin marketplace add RetrogradeLabs/lune
/plugin install lune@retrograde-labs-lune
/mcp                      # choose lune, then sign in
```

**Codex**

```
codex plugin marketplace add RetrogradeLabs/lune
codex plugin add lune@retrograde-labs-lune
codex mcp login lune
```

**VS Code and other Agent Plugins clients**

Point the client at `https://github.com/RetrogradeLabs/lune`. In VS Code that is
"Chat: Install Plugin From Source" in the Command Palette.

**Cursor**

Cursor installs plugins from a marketplace, and importing a repository is a team
setting: an admin adds this repo under Dashboard then Plugins, after which
everyone installs Lune from Customize in the sidebar.

## What you get

Twelve tools covering search, full text, citation trails, cross-paper extraction,
claim verification, and methodology lookup, plus three skills:

| Skill                       | Fires when                                                     |
| --------------------------- | -------------------------------------------------------------- |
| `lune-literature-review`    | "what work exists on X", related work, surveys, venue browsing |
| `lune-verify-draft`         | a draft or a set of claims needs fact-checking or sourcing     |
| `lune-research-methodology` | "how should I design / evaluate / structure / rebut"           |

Six slash commands ship with the MCP server itself: `/literature_review`,
`/find_related_work`, `/compare_papers`, `/verify_draft`, `/trace_citations`,
`/research_methodology`.

## Notes

**Sign-in.** `mcp.json` carries no credentials, by design and by spec. The client
runs OAuth discovery against `mcp.luneresearch.com` and stores the token itself, so
no token ever lands in a config file.

**If you already ran `lune install`.** Claude Code deduplicates MCP servers by URL,
and a manually configured server wins over a plugin's. Your existing setup keeps
working and the plugin's copy is skipped with a note. To switch to browser sign-in,
remove the `lune` entry from your MCP config first.

**Permission prompts.** `lune install` writes `mcp__lune__*` into your
`~/.claude/settings.json` allowlist. Plugin-provided tools are namespaced
`mcp__plugin_lune_lune__*` instead, which that entry does not cover, so a
plugin-only install prompts on first use of each tool.

## Development

The source of truth for this plugin lives in the Lune monorepo at
`packages/plugins/lune/`, and this repository is a sync target. Open issues and
pull requests against the monorepo rather than editing here.
