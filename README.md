# prompt-templates

Production prompts for working with LLMs. Each one is built around a specific failure mode of default model behavior: AI slop, sycophantic recommendations, shallow code review, unstructured output.

Tested with Claude Sonnet 4.6 and GPT-4o. Should port to any frontier model with minor adjustments.

## Usage

1. Pick the prompt that matches your task.
2. Copy the prompt block.
3. Replace `{{VARIABLES}}` with your inputs.
4. Send to your model.

No framework, no dependencies. Plain markdown.

## Index

| Prompt | Purpose | File |
|---|---|---|
| Anti-slop audit | Detect AI-generated cliche patterns in text | [`prompts/anti-slop-audit.md`](prompts/anti-slop-audit.md) |
| Tradeoffs then recommend | Force explicit tradeoffs before a recommendation | [`prompts/tradeoffs-then-recommend.md`](prompts/tradeoffs-then-recommend.md) |
| Code review pipeline | Five-stage code review with severity tags | [`prompts/code-review-pipeline.md`](prompts/code-review-pipeline.md) |
| Structured output JSON | Force strict JSON responses for programmatic use | [`prompts/structured-output-json.md`](prompts/structured-output-json.md) |

## Conventions

- Variables use `{{UPPERCASE}}` syntax.
- Each prompt declares its expected output format up front.
- Severity scales: `blocker / major / minor / nit`.
- Confidence scales: 1 (low) to 5 (high).

## Contributing

Pull requests welcome. Keep prompts:

- Specific (no vague instructions like "be detailed")
- Bounded (include length limits where it matters)
- Parseable (structured output beats prose)
- Tested (include a sample input and output if you can)

## License

MIT. See [LICENSE](LICENSE).
