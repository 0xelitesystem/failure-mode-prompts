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
| Anti-slop audit | Detect AI-generated cliche patterns in text | [`anti-slop-audit.md`](anti-slop-audit.md) |
| Tradeoffs then recommend | Force explicit tradeoffs before a recommendation | [`tradeoffs-then-recommend.md`](tradeoffs-then-recommend.md) |
| Code review pipeline | Five-stage code review with severity tags | [`code-review-pipeline.md`](code-review-pipeline.md) |
| Structured output JSON | Force strict JSON responses for programmatic use | [`structured-output-json.md`](structured-output-json.md) |

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

## More

Part of a catalog of single-file browser tools and plain-language references, all MIT licensed and dependency-free: [0xelitesystem.github.io](https://0xelitesystem.github.io/). Built by [elitesystem.ai](https://elitesystem.ai).

## License

MIT. See [LICENSE](LICENSE).
