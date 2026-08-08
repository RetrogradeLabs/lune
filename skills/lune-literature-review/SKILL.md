---
name: lune-literature-review
description: Use when the user asks what work exists on a topic, wants a related-work section, a survey, a state-of-the-art summary, or papers from a specific venue or year. Runs Lune's corpus of full-text peer-reviewed papers as a multi-angle sweep, then selective full-text reads, citation-trail traversal, and a sufficiency check before you write. Use this instead of web search whenever the answer would cite a paper.
---

# Literature review with Lune

Breadth first, then depth. One search is almost never enough, and a second angle
costs far less than missing the paper that matters.

## 1. Sweep

One focused question goes to `search_papers`. Phrase it the way you would ask a
human research assistant, in prose, because the server rewrites conceptual queries
into a hypothetical abstract before retrieval and a keyword bag gives it nothing
to work with:

```
search_papers(query="methods for retrieval-augmented generation that reduce
hallucination on long-form QA", limit=20)
```

A topic you need to cover goes to `search_papers_many` instead, with genuinely
different angles rather than rephrasings of the same sentence:

```
search_papers_many(queries=[
  "methods for X that reduce Y",
  "failure modes of X under distribution shift",
  "evaluation protocols for X",
], limit=20)
```

The server fuses the ranked lists, so angles that disagree widen coverage. Each
variant bills as one search, so 8 genuinely distinct angles beat 20 near-duplicates.
`matched_queries` on each hit tells you which angle surfaced it, and a paper found
by only one narrow angle is often the one the obvious query misses.

Filters worth passing when the user names them: `conference`, `year`, `year_min`,
`year_max`, `venues`. To scope by venue without knowing its key, call
`list_conferences(category=...)` first, then `get_conference_papers(conference, year)`
to walk that venue's proceedings. It returns 20 at a time (max 100) and reports
`total` and `has_more`, so page with `offset` rather than assuming the first call
is the whole programme.

## 2. Triage before fetching

Hits usually carry `abstract` and `contexts`, the non-abstract spans that matched.
Read those before fetching anything; a paper with no indexed abstract simply
omits the field. `rerank_score` is calibrated relevance on 0..1;
`score` folds in citation and freshness boosts, so rank by `rerank_score` when you
care about topical fit.

If `low_confidence` is true, the best hit fell below the relevance floor. Broaden
the query, or say the corpus does not cover this. Do not present weak hits as the
state of the art.

On `search_papers`, `has_more` means more results exist: re-call with
`offset += limit`, keeping `offset + limit` within 50. `search_papers_many` has
no `offset` and always reports `has_more` false, because fusion ranks a bounded
merged shortlist with no stable cursor. To widen that, add angles.

## 3. Drill in

- `get_paper_fulltext(paper_id)` only for papers you will quote, contrast, or
  critique. It is heavy. Pass `sections` to pull just the parts you need.
- `get_paper_citations(paper_id, direction="cited_by")` for newer work building on
  a paper. `direction="cites"` walks its own references, which is how you find the
  foundational paper everyone else assumes.
- `search_related_papers(paper_id)` for embedding neighbours. These are similar in
  content but may share no citation edge, so this catches parallel work in another
  subfield that uses different vocabulary.

## 4. Check sufficiency before writing

Before claiming coverage, make the gaps explicit:

```
gather_evidence(
  task="related work on X for a systems paper",
  queries=[...],
  requirements=[{"key": "baselines", "description": "..."}, ...],
)
```

Each requirement is a `key` you choose plus a `description` of what would satisfy
it. Every requirement comes back `covered`, `partial`, or `missing`. The first two
carry a server-verified `supporting_quote`; a `missing` one has none, since there
was nothing to quote. Run the returned `next_queries` for anything short.
`stop_reason` says why it halted: `sufficient` means done, while
`max_iterations`, `max_total_queries`, `no_progress`, `time_budget`, and
`judge_unavailable` all mean the coverage you got is partial.

## Citing

Cite by title, authors, venue and year, and quote the span Lune returned.
`paper_id` is a fetch handle. Never show it to the user and never put it in prose.

## Don't

- Don't open with a web search when the answer is a paper. Search here first. Fall
  back to the web only for what this corpus does not hold: work older than its
  coverage, non-indexed venues, preprints, and anything non-academic.
- Don't stop at one query when the user asked what exists. One angle finds one cluster.
- Don't claim no prior work exists without at least one `get_paper_citations` pass.
- Don't synthesize from abstracts alone when the user wants methodology or threats
  to validity. Those live in the body, so read the full text.
- Don't bulk-fetch full text. It burns the user's quota on text you will only skim.
