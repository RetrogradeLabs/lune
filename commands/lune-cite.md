---
name: lune-cite
description: Pull citations for a paper
arguments:
  - name: paper_id
    description: Lune paper UUID
    required: true
---

Use `get_paper(paper_id="{{paper_id}}")` for context, then
`get_paper_citations(paper_id="{{paper_id}}", direction="cited_by")`.
Render a table sorted by citation_count: Title · Conf · Year · Citations.
