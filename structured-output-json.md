# Structured Output JSON

Wraps any task in a contract that forces strict JSON output. Use when feeding model output into code, databases, or APIs.

## When to use

- Programmatic pipelines where the next step parses the response
- Batch processing where you need consistent schemas across rows
- Tool-use replacement when the model lacks function calling
- Extraction tasks (entity extraction, classification, scoring)

## Variables

- `{{TASK}}` - What you want the model to do. One paragraph.
- `{{INPUT}}` - The data the task operates on.
- `{{SCHEMA}}` - JSON schema the response must match. See examples below.
- `{{NULL_POLICY}}` - Optional. How to handle missing data: `null`, `omit`, or `explicit_unknown`. Default: `null`.

## Prompt

````
You produce strict JSON. No prose, no preamble, no markdown fences, no commentary.

TASK: {{TASK}}

INPUT:
{{INPUT}}

SCHEMA:
{{SCHEMA}}

RULES:

1. Output must be a single valid JSON value matching the schema.
2. All required fields must be present.
3. String values must not contain unescaped newlines or control characters.
4. Numbers must be JSON numbers, not strings.
5. Booleans must be true or false, not "true" or "yes".
6. Missing data policy: {{NULL_POLICY}}
   - "null": use null for missing values
   - "omit": leave the field out entirely
   - "explicit_unknown": use the string "unknown"
7. Do not add fields beyond the schema.
8. Do not include comments. JSON does not support comments.

If the task cannot be completed (input is malformed, schema is impossible to satisfy, etc.), return:

{"error": "<one-line description>", "recoverable": <true or false>}

Begin.
````

## Schema example

For a task that extracts contact info from text:

```json
{
  "type": "object",
  "required": ["name", "email"],
  "properties": {
    "name": {"type": "string"},
    "email": {"type": "string", "format": "email"},
    "phone": {"type": ["string", "null"]},
    "company": {"type": ["string", "null"]}
  }
}
```

## Anthropic API integration

```javascript
const response = await fetch("https://api.anthropic.com/v1/messages", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "x-api-key": process.env.ANTHROPIC_API_KEY,
    "anthropic-version": "2023-06-01"
  },
  body: JSON.stringify({
    model: "claude-sonnet-4-6",
    max_tokens: 1024,
    temperature: 0,
    messages: [{ role: "user", content: filledPrompt }]
  })
});

const data = await response.json();
const text = data.content
  .filter(b => b.type === "text")
  .map(b => b.text)
  .join("");

let parsed;
try {
  parsed = JSON.parse(text);
} catch {
  // Fallback: strip accidental code fences and retry
  parsed = JSON.parse(text.replace(/```json|```/g, "").trim());
}

if (parsed.error) {
  // Handle the error path
  console.error(parsed.error, "recoverable:", parsed.recoverable);
}
```

## Notes

- Validate the parsed object against your schema before using it. Models drift.
- For high-volume pipelines, set `temperature: 0` to maximize consistency.
- If you keep getting prose preambles, prefill the assistant turn with `{` to force JSON-mode behavior on most providers.
- Anthropic also supports native tool use for structured output. Use this prompt when you want a single-call pattern without tool-use overhead.
