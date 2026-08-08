---
name: lune-research-methodology
description: Use BEFORE advising on experimental design, ablations, baselines, evaluation metrics and protocols, statistical tests, reproducibility, paper structure, related-work organisation, venue choice, or how to answer reviewers. Queries Lune's curated methodology corpus, drawn from senior researchers, reproducibility checklists, and venue reviewer guidance, so the advice is anchored in vetted sources rather than a plausible guess. Use whenever the question starts with "how should I".
---

# Grounding methodology advice

Methodology questions are the ones where a confident wrong answer is most costly
and hardest for the user to detect. This corpus is curated, not the open web:
reproducibility checklists, senior-researcher writeups on common pitfalls,
venue-specific reviewer guidance, and rebuttal practice.

## Search first, advise second

Call `search_research_guidance` before forming an opinion, not after, so you are
not just looking for support for an answer you already drafted:

```
search_research_guidance(query="how to design ablations that isolate a single component")
```

Phrase the query the way the user's problem actually reads. Hits come back with
`doc_title`, `section`, and the matched `excerpt`, often enough to answer from.
`source_url` is present on most but not all documents.

Fetch the document when you need the full argument, a checklist end to end, or
the author:

```
get_research_guidance_doc(doc_id)
```

That returns `content` (the reassembled body) plus `sections` split by heading, so
you can follow a checklist in order rather than quoting a fragment out of context.

## Citing guidance

Guidance documents are not papers and have no venue. Cite a search hit by
`doc_title`, linking `source_url` when the hit carries one. `author` and
`author_affiliation` exist only on the fetched document, so name an author only
when you actually fetched it. `doc_id` is a fetch handle, never shown to the user.

## When the corpus is silent

If nothing relevant comes back, say so in the answer rather than closing the gap
with generic advice:

> Lune's curated guidance doesn't cover this directly. Here's my reasoning, but
> treat it as my reasoning rather than something vetted.

Flagging the gap is the useful signal. The point of the tool is telling the user
when vetted guidance actually exists, which means being equally clear when it
does not. The corpus returns what individual documents say; it does not measure
agreement between them, so report a single document as one source's view rather
than as settled practice.

## Don't

- Don't search the guidance and then ignore it. Either ground the advice in what
  came back or skip the call and say you are reasoning from first principles.
- Don't flatten a specific recommendation into general advice. Quote or summarize
  the specifics and attribute them.
- Don't cite guidance docs as if they were papers, with a venue and year they
  don't have. For empirical claims about what works, use the paper corpus instead.
