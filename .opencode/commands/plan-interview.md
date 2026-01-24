---
description: Interview me to expand a spec from prompt
agent: build
---

You will take a prompt about a feature/project and interview me to create an implementation-ready spec.

Prompt:
$ARGUMENTS

Rules:
- Use the question tool for asking questions.
- Ask non-obvious questions about literally anything: technical implementation, UI & UX, edge cases, tradeoffs, and acceptance criteria.
- Be very in-depth and continue interviewing me until the spec is complete.

Before writing:
- Summarize the final spec outline and ask me to confirm.
- After confirmation, check if in a git repository:
  - If yes: create a new git branch with prefix 'feat/' and a short summarization of the prompt (3-5 words, lowercase, hyphens-only)
  - If no: skip branch creation and note that git branch creation requires a git repository

Then:
- Write the final spec to `plan.md` (keep it well-structured with headings).
