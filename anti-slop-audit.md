# Anti-Slop Audit

Detects AI-generated cliche patterns in text content. Returns structured findings with severity tags.

## When to use

- Reviewing copy for landing pages, blog posts, or product descriptions
- Auditing model outputs before publishing
- Filtering submissions in content workflows
- QA pass on documentation

## Variables

- `{{CONTENT}}` - The text to audit. Plain text or markdown.
- `{{CONTEXT}}` - Optional. Intended audience or use case (default: general).

## Prompt

````
You are a copy editor specializing in detecting AI-generated cliches. Audit the content below against the rules in each category. Return a structured report.

CONTENT:
{{CONTENT}}

CONTEXT: {{CONTEXT}}

CATEGORIES:

1. BANNED VOCABULARY (any occurrence = major)

Words and phrases that signal generic AI output:

- delve, delving
- tapestry, rich tapestry
- robust (when used for non-statistical claims)
- leverage (as a verb)
- seamless, seamlessly
- navigate the landscape, the landscape of
- in today's fast-paced world, in today's digital age
- in the realm of
- ever-evolving, ever-changing
- unprecedented (unless quantitatively true)
- paradigm shift
- journey (as metaphor for process)
- embark on
- unlock the power of
- elevate your, elevate the
- harness the power of
- foster (as verb for creating)
- dive deep, dive into
- it's worth noting that
- it's important to remember
- when it comes to
- at the end of the day
- in conclusion
- ultimately

2. STRUCTURAL PATTERNS (count occurrences, threshold = major)

- Em-dashes: more than 2 per 200 words
- "Not just X, but Y" construction: more than 1 per 500 words
- Bullet density: more than 50% of content as bullet points
- Sentence-initial "Furthermore", "Moreover", "Additionally": more than 2 in same section
- Tricolon abuse: more than 2 three-item lists in same paragraph

3. HEDGING STACK (any stack of 3 or more in the same paragraph = minor)

- it's worth noting
- it's important to remember
- one could argue
- generally speaking
- in many cases
- often, frequently, typically (when stacked)

4. EMOJI CLUTTER (any usage in body text = nit; headers OK if intentional)

5. EXCLAMATION OVERUSE (more than 1 per 300 words = minor)

OUTPUT FORMAT:

Return JSON with this exact shape:

{
  "verdict": "pass" or "fail",
  "severity_summary": {
    "blocker": <int>,
    "major": <int>,
    "minor": <int>,
    "nit": <int>
  },
  "findings": [
    {
      "category": "<one of the 5 categories>",
      "severity": "blocker | major | minor | nit",
      "quote": "<exact text from input>",
      "issue": "<one-line explanation>",
      "suggestion": "<concrete rewrite>"
    }
  ],
  "passes": ["<categories with no findings>"]
}

Verdict is "fail" if there is any blocker or 3 or more majors. Otherwise "pass".

Return only the JSON. No preamble, no commentary, no code fences.
````

## Expected output

A JSON object. Findings sorted by severity descending. Empty `findings` array means clean content.

## Notes

- The banned vocabulary list is opinionated. Trim or extend it for your context.
- Tighten or loosen thresholds in section 2 based on your style guide.
- For longer documents, audit in 1500-word chunks. Most models lose precision past that.
- Pair with `structured-output-json.md` if you need stricter parsing guarantees.
