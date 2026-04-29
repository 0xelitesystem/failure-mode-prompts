# Tradeoffs Then Recommend

Forces explicit tradeoff analysis before a recommendation. Combats sycophancy, premature conclusions, and "it depends" hedging.

## When to use

- Choosing between frameworks, libraries, or services
- Architecture decisions
- Vendor or product comparisons
- Hiring decisions between final candidates
- Any decision where the model would default to "both have merits"

## Variables

- `{{DECISION}}` - One sentence describing the choice you are making.
- `{{OPTIONS}}` - List of options to compare. Minimum 2.
- `{{CRITERIA}}` - Optional. Comma-separated list of factors that matter. If omitted, the model derives them.
- `{{CONSTRAINTS}}` - Optional. Hard requirements that disqualify options.

## Prompt

````
You are an analyst making a structured recommendation. Avoid hedging. Avoid both-sides framing. If two options are genuinely close, say so explicitly with a confidence score.

DECISION: {{DECISION}}

OPTIONS:
{{OPTIONS}}

CRITERIA: {{CRITERIA}}

HARD CONSTRAINTS: {{CONSTRAINTS}}

Process:

STEP 1. Restate the decision in your own words. Flag any ambiguity that would change the answer.

STEP 2. List 4 to 7 evaluation criteria. If criteria were provided, use those. Assign each a weight from 1 to 5 based on how much it should influence the decision. Total weights must sum to a number between 15 and 25.

STEP 3. For each option, score 1 to 5 against each criterion. Compute the weighted total.

STEP 4. For each option, write one sentence describing what you give up by choosing it. This is the tradeoff. No option escapes this step.

STEP 5. State the recommendation. Cite the weighted score, but do not let the score override clear qualitative concerns. Explain why.

STEP 6. State a flip condition: one specific change in inputs or context that would make a different option the winner.

STEP 7. Confidence: 1 (coin flip) to 5 (clear winner).

OUTPUT FORMAT:

## Decision
<restated decision>

## Ambiguities
<list, or "none">

## Criteria
| Criterion | Weight | Why it matters |
|---|---|---|

## Scoring
| Option | <criterion 1> | <criterion 2> | ... | Weighted Total |
|---|---|---|---|---|

## Tradeoffs
- <Option A>: <what you give up>
- <Option B>: <what you give up>

## Recommendation
<option name>

<2 to 4 sentences explaining why, including any qualitative override of the score>

## Flip Condition
If <specific input change>, the recommendation becomes <other option>.

## Confidence
<1 to 5> / 5
````

## Expected output

Markdown matching the format above. The flip condition is the most important field. If a model refuses to commit to one, it has not done the analysis.

## Notes

- For high-stakes decisions, run this prompt twice with the options listed in opposite order. If the recommendation flips between runs, the analysis is unstable.
- Pair with `structured-output-json.md` if you need to parse the result programmatically.
- The weight sum constraint (15 to 25) prevents the model from flattening criteria with all-equal weights.
