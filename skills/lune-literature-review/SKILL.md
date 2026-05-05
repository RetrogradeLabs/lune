---
name: lune-literature-review
description: Use when the user asks for a literature review, related-work summary, or "what's the state of the art on X". Multi-step pattern: search → read top abstracts → traverse citation graph → synthesize.
---

# Doing a literature review with Lune

A good lit review uses a **breadth-first search → depth-first read** pattern.

## Step 1: Cast the net

```
search_papers(query="<topic>", limit=20)
```

Skim AI TL;DRs to identify the 3–5 most-cited or most-on-topic papers.

## Step 2: Pull full metadata for the top picks

For each top paper, call `get_paper(paper_id)` — gives you abstract, keywords, citation_count.

## Step 3: Traverse the citation graph

For each top paper, call `get_paper_citations(paper_id, direction='cited_by')` to find newer work that builds on it. This catches papers that don't surface for the original query but extend the line of work.

## Step 4: Read full text only when needed

Use `get_paper_fulltext` only for papers you'll directly quote, contrast, or critique. Don't bulk-download full text — it's expensive on the user's quota.

## Step 5: Synthesize

When summarizing, **always cite by paper_id** so the user can deep-link via `https://luneresearch.com/papers/{id}` (or you can suggest `lune papers get {id}` in the CLI).

## Don't

- Don't synthesize from abstracts alone if the user asks for technical detail — abstracts hide methodology and threats to validity. Use full text for that.
- Don't claim "no prior work exists" without traversing at least one round of `get_paper_citations`.
