# Code Review Pipeline

Five-stage code review. Each stage runs independently with its own checklist and severity tags. Designed to plug into CI as a structured comment generator.

## When to use

- Pull request review
- Pre-merge audit on solo projects
- Quality gate before deploying to production
- Onboarding code review for new contributors

## Variables

- `{{LANGUAGE}}` - Primary language. Used to load language-specific checks.
- `{{DIFF}}` - The diff or full file contents to review. For large changes, review file by file.
- `{{CONTEXT}}` - Optional. What the change is supposed to accomplish.

## Prompt

````
You are reviewing code changes through a five-stage pipeline. Each stage has its own checklist. Run them in order. Do not skip stages.

Severity tags:
- blocker: must fix before merge
- major: fix before next release
- minor: improve when convenient
- nit: style preference

LANGUAGE: {{LANGUAGE}}
CONTEXT: {{CONTEXT}}

CODE:
{{DIFF}}

STAGE 1. ARCHITECTURE
- Are module boundaries respected?
- Is coupling between components appropriate?
- Are there leaky abstractions?
- Does this change introduce circular dependencies?
- Is the layer of abstraction consistent within each function?

STAGE 2. QUALITY
- Are names clear and specific (not "data", "info", "manager")?
- Is there duplication that should be extracted?
- Is there complexity that should be inlined (KISS)?
- Dead code, unreachable branches, commented-out code?
- Inconsistent error handling within the same function?
- Magic numbers or strings that should be named constants?

STAGE 3. TESTS
- Are new code paths covered?
- Are edge cases tested (empty, null, boundary, max)?
- Do assertions check behavior, not implementation details?
- Are tests isolated (no shared mutable state)?
- Are test names descriptive of what they verify?
- Any tests that could pass against incorrect code?

STAGE 4. PERFORMANCE
- Algorithmic complexity reasonable for input size?
- N+1 queries or redundant database round trips?
- Unbounded memory growth (lists, caches, accumulators)?
- Missing caching where output is deterministic and expensive?
- Synchronous operations that could be async?
- Hot paths doing unnecessary allocations?

STAGE 5. SECURITY
- Input validation on all external inputs?
- SQL injection, command injection, path traversal vectors?
- Authentication and authorization on every endpoint?
- Secrets in code, logs, error messages, or commits?
- Cryptographic primitives used correctly (no MD5 for security, no ECB mode, proper IV handling)?
- CSRF, XSS, SSRF surface in web code?
- Race conditions on shared state?

OUTPUT FORMAT:

## Stage 1: Architecture
| Severity | File:Line | Finding | Fix |
|---|---|---|---|
| <tag> | <ref> | <one line> | <one line> |

(Repeat the same table format for stages 2, 3, 4, and 5.)

## Summary
- Blockers: <count>
- Majors: <count>
- Minors: <count>
- Nits: <count>

## Verdict
<APPROVE | REQUEST_CHANGES | COMMENT>

Verdict rules:
- APPROVE only if zero blockers and zero majors.
- REQUEST_CHANGES if any blocker or 3 or more majors.
- COMMENT otherwise.

If a stage has zero findings, write "No findings." under that stage heading.

Do not invent issues. If the code is clean, say so.
````

## Expected output

Markdown report with one table per stage, a summary, and a verdict. Empty stages report "No findings."

## Notes

- Run this on diffs for changesets under 500 lines. For larger changes, review file by file.
- The "Do not invent issues" instruction matters. Without it, models hallucinate findings to look thorough.
- For automated CI integration, wrap with `structured-output-json.md` to get parseable output.
- The pipeline order (architecture first, security last) is intentional. A bad architecture cannot be fixed by patching individual security holes.
