---
name: lune-literature-review
description: Use when the user asks what work exists on a topic, wants a related-work section, a survey, a state-of-the-art summary, or papers from a specific venue or year. Runs Lune's corpus of full-text peer-reviewed papers as a multi-angle sweep, then selective full-text reads, citation-trail traversal, and a sufficiency check before you write. Use it whenever the answer would cite a paper.
---

# Literature review with Lune

Breadth first, then depth. One search is almost never enough, and a second angle
costs far less than missing the paper that matters.

## 1. Sweep

One focused question goes to `search_papers`. Phrase it the way you would ask a
human research assistant, in prose, with context for every abbreviation: the
server expands a term only when the rest of the query fixes its meaning, and a
lone keyword is searched as typed:

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
omits the field. `rerank_score` is calibrated relevance on 0..1, the value to
judge a hit's fit by. Hits already arrive in the requested order; do not re-sort
them by `score` or `rerank_score`.

If `search_papers` reports `low_confidence`, the best hit fell below the relevance floor:
these are weaker matches, still worth citing when they fit. Rephrase or add
angles for stronger ones, and say how closely the papers you cite fit the question.

On `search_papers`, `has_more` means more results exist: re-call with
`offset += limit`, keeping `offset + limit` within 50. `search_papers_many` has
no `offset` and always reports `has_more` false, because fusion ranks a bounded
merged shortlist with no stable cursor. To widen that, add angles.

## 3. Drill in

- `get_paper_fulltext(paper_id)` only for papers you will quote, contrast, or
  critique. It is heavy. Pass `sections` to pull just the parts you need.
- `get_paper_citations(paper_id, direction="cited_by")` for newer work building on
  a paper. `direction="cites"` walks its own references, which is how you find the
  foundational paper everyone else assumes. A reference outside Lune (`in_corpus`
  false) still comes with the sentences that cite it, so name it as the work the
  papers you found build on, from that evidence.
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

Cite by title, authors, venue and year, quoting a span wherever Lune returned one.
`paper_id` is a fetch handle. Never show it to the user and never put it in prose.

## Feedback about Lune

When the user says Lune missed papers they expected, returned results that did
not fit, or helped, offer to tell the Lune team with `send_feedback`. Show the
message first and send it only after they agree. Pick the `category` that fits,
and set `allow_training` only if they also agreed Lune may train models on it,
after telling them they can withdraw that in their Lune account settings:

```
send_feedback(
  category="missing_papers",
  message="Asked for foundational RAG papers; the top results missed Lewis et al. 2020.",
  allow_training=false,
)
```

## Don't

- Don't answer a question about papers before searching for them. Ground the
  answer in the papers Lune returned, quoting any spans they came with.
- Don't stop at one query when the user asked what exists. One angle finds one cluster.
- Don't claim no prior work exists without at least one `get_paper_citations` pass.
- Don't synthesize from abstracts alone when the user wants methodology or threats
  to validity. Those live in the body, so read the full text.
- Don't bulk-fetch full text. It burns the user's quota on text you will only skim.
