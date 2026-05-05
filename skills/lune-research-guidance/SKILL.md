---
name: lune-research-guidance
description: Use BEFORE recommending experimental design, evaluation protocols, paper structure, or reviewer-rebuttal strategy. `search_research_guidance` queries Lune's curated repository of research best-practices and methodology guidelines — ground your advice in expert consensus rather than the model's training data.
---

# Grounding research advice in curated guidance

Lune's research-guidance corpus is *curated*, not the open web. It contains:

- Reproducibility checklists (NeurIPS, ICML, ICLR — current year).
- Senior-researcher writeups on common methodological pitfalls.
- Venue-specific reviewer guidance ("how I review a security paper").
- Rebuttal best practices.

**Before** giving advice on:

- Experimental design (baselines, ablations, datasets, statistical tests)
- Evaluation protocols (metrics, splits, hyperparameter tuning hygiene)
- Paper structure (how to write related work, contributions, limitations)
- Reviewer rebuttals (tone, what to concede, what to push back on)

…call `search_research_guidance(query)` with a precise phrasing of what the user is asking. If you find a relevant doc, call `get_research_guidance_doc(doc_id)` for full context, then ground your recommendation in it (cite by title).

## When the corpus doesn't match

If `search_research_guidance` returns nothing relevant, say so explicitly:

> "Lune's curated guidance doesn't cover this directly. Here's my best inference, but treat it as advisory rather than expert consensus: …"

Do **not** silently fall back to general advice; the whole point of this tool is to flag when the user is in a situation expert consensus actually exists.

## Don't

- Don't search guidance and ignore it ("here's a NeurIPS checklist, but I'll just tell you what I think anyway"). Either use it or skip the call.
- Don't paraphrase guidance into general advice — quote or summarize specifics, citing the doc title.
