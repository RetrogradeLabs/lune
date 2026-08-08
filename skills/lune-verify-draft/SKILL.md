---
name: lune-verify-draft
description: Use when the user pastes a draft, abstract, paragraph, or list of claims and wants it fact-checked, sourced, or citation-checked, or asks whether something is actually true in the literature. Checks each claim against Lune's peer-reviewed corpus and returns a supported, unsupported, or insufficient-evidence verdict, the first two carrying a verbatim quote from the paper that settles it. Also builds side-by-side comparison tables across a set of papers. Use this before asserting any research fact you would otherwise state from memory.
---

# Checking a draft against the corpus

The job is to separate what the literature actually supports from what merely
sounds right. Do not restate the draft with citations bolted on.

## 1. Decompose into atomic claims

Split the draft into individually checkable statements. One claim per assertion,
not one per paragraph: a sentence carrying three assertions returns one blurred
verdict instead of three sharp ones. Strip hedges and pronouns so each claim
stands alone.

```
verify_claims(
  claims=[
    "Batch normalization reduces internal covariate shift.",
    "Method X outperforms Y on ImageNet at equal parameter count.",
  ],
  context="draft intro of a paper on training dynamics",
)
```

`context` is worth passing: it disambiguates terms that mean different things in
different subfields. Filters (`conference`, `year_min`, `venues`) narrow the
evidence pool when the user cares about a specific literature.

## 2. Read the verdicts honestly

Each verdict is one of three states, and the third is not a soft no:

- `supported`: a retrieved passage directly substantiates the claim.
- `unsupported`: a retrieved passage directly contradicts it. Say so plainly and
  quote the contradiction. This is the finding the user actually needs.
- `insufficient_evidence`: the corpus neither confirms nor denies it. Report it as
  unsettled, never as refuted, and never quietly drop the claim.

`verbatim_quote` is checked server-side against a passage from one of the
`supporting_paper_ids` (matched with whitespace normalised), so quote it directly
rather than paraphrasing.
That substring check is the guarantee. A `supported` or `unsupported` verdict
whose quote fails it is downgraded to `insufficient_evidence` with the quote
dropped, which is why those two verdicts always arrive citable. `confidence` is
the judge's own confidence on 0..1 and qualifies the verdict; it is not a
relevance score, and `low_confidence` does not exist here (that field belongs to
search).

Report per claim. A draft where 8 claims are supported and 2 are unsupported is not
"mostly fine": lead with the 2.

## 3. Compare across the papers involved

When the draft asserts a contrast between approaches, build the table rather than
asserting it. Search first to get the papers, then extract the same fields from
each so the comparison is apples to apples:

```
extract_from_papers(
  paper_ids=[...],
  instruction="compare the evaluation setup of each method",
  fields=[
    {"name": "dataset", "type": "string", "description": "evaluation dataset"},
    {"name": "baselines", "type": "string[]", "description": "baselines compared against"},
  ],
)
```

Each field is a `name`, a `type`, and a `description` of what to pull. Up to 50
papers and 12 fields per call. `type` is `string`, `number`, `boolean`, or
`string[]`. Pass `sections` to focus extraction when the answer lives in a known
part of the paper.

## Citing

Cite by title, authors, venue and year, and surface the verbatim quote. The
`supporting_paper_ids` are fetch handles for `get_paper_fulltext`, never output.

## Don't

- Don't state a research fact from memory when the user is asking whether it holds.
  That is the entire reason this tool exists.
- Don't report `insufficient_evidence` as if it were `unsupported`. One means the
  corpus is silent; the other means the corpus disagrees.
- Don't paraphrase `verbatim_quote`. It is verified verbatim, so paraphrasing throws
  away the only guarantee you have.
- Don't send a whole paragraph as one claim. Split it.
- Don't pad the claim list to look thorough. Each claim bills, and 25 is the ceiling.
