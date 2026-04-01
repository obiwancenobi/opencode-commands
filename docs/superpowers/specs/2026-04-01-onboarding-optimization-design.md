# Onboarding Command Optimization — Design Spec

**Date:** 2026-04-01
**Scope:** Mermaid diagram reliability + two-stage HTML generation
**Files to modify:** 4

## Problem

1. Mermaid diagrams in the generated onboarding HTML sometimes fail to render or show errors.
2. The single massive HTML write (1500-2000+ lines) is fragile — the LLM can corrupt closing tags, mangle CSS, or truncate content.

## Solution: Hybrid Approach

Keep the single self-contained HTML output (portability is the key feature) but improve reliability through two changes:

### Change 1: Mermaid Diagram Reliability

**A) Add syntax constraints to all 5 subagent prompts.**

Add the following rules to every subagent prompt that produces a Mermaid diagram (Subagents 1-5):

```
Mermaid syntax rules — follow these strictly:
- Wrap ALL node labels in double quotes: A["My Label"] not A[My Label]
- No special characters outside quoted labels (no parentheses, colons, ampersands in bare text)
- Use only these diagram types: sequenceDiagram, flowchart, classDiagram
- No subgraphs with special characters in titles
- Keep diagrams under 20 nodes — simplify if needed
- Test mentally: does every arrow have a valid source and target?
- If unsure about syntax, use a simpler diagram rather than a complex broken one
```

**B) Modernize the HTML Mermaid initialization.**

Replace the current fragile init block (manual DOM manipulation + deprecated `mermaid.init()`) with:

```html
<script src="https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.min.js"></script>
<script>
  mermaid.initialize({
    startOnLoad: true,
    theme: 'dark',
    securityLevel: 'loose'
  });
  mermaid.parseError = function(err, hash) {
    console.warn('Mermaid parse error:', err);
  };
  window.addEventListener('load', function() {
    document.querySelectorAll('.mermaid[data-processed="true"]').forEach(function(el) {
      if (!el.querySelector('svg')) {
        el.classList.add('mermaid-error');
        el.setAttribute('title', 'Diagram could not render — showing source');
      }
    });
  });
</script>
```

Key differences from current:
- Pins to Mermaid v11 (stable) instead of `latest`
- Uses `startOnLoad: true` — no manual `querySelectorAll` + element replacement needed
- `parseError` handler prevents one broken diagram from killing others
- Post-load check adds `.mermaid-error` class for CSS fallback styling

**C) Add error fallback CSS and noscript.**

Add to the HTML template CSS:

```css
.mermaid-error {
  border: 1px dashed var(--secondary);
  padding: 1rem;
  font-family: monospace;
  font-size: 0.85rem;
  white-space: pre-wrap;
  opacity: 0.7;
}
.mermaid-error::before {
  content: "Diagram could not render — raw source shown below";
  display: block;
  margin-bottom: 0.5rem;
  color: var(--accent);
  font-family: sans-serif;
}
```

Add noscript fallback:

```html
<noscript>
  <style>.mermaid { white-space: pre-wrap; font-family: monospace; }</style>
  <p>JavaScript is required for interactive navigation and diagram rendering.</p>
</noscript>
```

### Change 2: Two-Stage HTML Generation

Replace the current single-step "generate entire HTML" with two stages.

**Stage A: Assemble tour data as JSON.**

After all 5 subagents complete, write structured tour data to `docs/_onboarding-data.json`:

```json
{
  "projectName": "string",
  "language": "string",
  "framework": "string",
  "description": "string",
  "dependencies": ["string"],
  "commands": {
    "build": "string",
    "dev": "string",
    "test": "string"
  },
  "steps": [
    {
      "title": "string",
      "filePath": "string (e.g. src/index.ts:1-30)",
      "narrative": "string (2-4 paragraphs)",
      "code": "string (10-50 lines)",
      "highlightLines": [1, 5, 8],
      "keyPoints": ["string"],
      "diagram": "string (raw Mermaid source, or null)"
    }
  ],
  "architectureDiagram": "string (raw Mermaid source, or null)",
  "dataFlowDiagram": "string (raw Mermaid source, or null)",
  "integrationDiagram": "string (raw Mermaid source, or null)"
}
```

**Stage B: Generate HTML from the JSON data.**

Read `docs/_onboarding-data.json` and generate `{OUTPUT_FILE}`. The HTML generation is now template-filling — the LLM just needs to produce the HTML shell and interpolate data from the JSON. This is much more reliable than generating everything from scratch.

**Cleanup:** Delete `docs/_onboarding-data.json` after HTML is written.

## Files to Modify

| # | File | Changes |
|---|------|---------|
| 1 | `.claude/commands/generate-onboarding.md` | Add Mermaid rules to subagent prompts, replace Mermaid init block, add error CSS/noscript, add two-stage generation (JSON then HTML), add cleanup step |
| 2 | `.opencode/commands/generate-onboarding.md` | Same changes adapted to OpenCode syntax |
| 3 | `.kilocode/workflows/generate-onboarding.md` | Same changes adapted to Kilo Code syntax |
| 4 | `docs/generate-onboarding-workflow.md` | Update pipeline diagram to show two-stage flow, update Mermaid init reference |

## What Does NOT Change

- Output is still a single self-contained HTML file
- Still 5 parallel subagents for research
- Same discovery phase (Step 1-2)
- Same tour structure (Project Overview + execution flow steps)
- Same HTML layout, theme, navigation, code blocks, print styles
- Same error handling for missing entry points, failed subagents, large repos
- README.md, AGENTS.md, CLAUDE.md unchanged
