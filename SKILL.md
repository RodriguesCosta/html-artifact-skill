---
name: html-artifact
description: Create rich single-file HTML artifacts through a short discovery conversation. Trigger when the user asks for an HTML file, "html artifact", mockup, prototype, slide deck, interactive editor, status report, code walkthrough, design exploration, explainer page, flowchart/diagram, or any visual one-off doc that's richer than markdown. Also trigger when the user wants a richer alternative to a markdown plan/spec/report, mentions "explore options side-by-side", "build me a throwaway editor for X", "make me a tool to triage Y", or wants visual specs, mockups, or interactive tools to stay in the loop while Claude does work. The skill runs a 2-3 question discovery before writing a single self-contained .html file with no external dependencies — ask first, generate second.
---

# HTML artifact skill

## Why this exists

Markdown is the default agent output, but for anything with structure, visuals, or interactivity, HTML is far richer and more readable. The trap: someone says "make me an HTML file" with no clear idea of what they want, and you produce a generic doc that fails them.

**This skill exists to pull the spec out of the user before writing a line of HTML.** The conversation is the product. The generation is the easy part. If you find yourself generating something generic, you skipped the conversation — go back.

Match the user's language during the conversation (Portuguese in / Portuguese out, etc.). The HTML output itself follows the user's content language.

## The flow

A short discovery — **at most 3 short questions** — then a one-line spec recap, then generation. Prefer `AskUserQuestion` when available (the user picks from concrete options; "Other" is always available for free-form). When it isn't, ask a single concise question in chat.

If the user already specified a detail in their original message, don't re-ask — use it. The goal is to fill the genuine gaps, not collect a form.

### Round 1 — Intent

Skip this round if the user's request already names the category (e.g. "make me a slide deck about X", "build a feature flag editor for our config").

Otherwise ask (4 options via AskUserQuestion):

- **Compare / explore options** — multiple variants side-by-side for picking a direction
- **Explain or report** — narrative doc with diagrams, code, headings (code review, explainer, status report, plan, deck)
- **Throwaway editor or prototype** — interactive widget with a copy / export button at the end
- **Diagram or illustration** — pure visual: SVG flowchart, dependency graph, mind map

### Round 2 — Specifics

Pick **one** drill-down based on the bucket. For other unspecified details, default smartly and mention the defaults in the recap — the user can correct.

| Bucket | Most useful drill-down |
|---|---|
| Compare/explore | How many variants? (default 4) — what dimension varies (layout, tone, density, approach) |
| Explain/report | Who's the reader, and how deep? (skim ~5min / deep dive / both via tabs) |
| Editor/prototype | What does the export button produce? (markdown / JSON / paste-back-into-Claude prompt) |
| Diagram | What kind? (flowchart / sequence / dependency graph / mind map / annotated timeline) |

For reports specifically, also ask the data source on the same round: paste it now, read from files, pull from git history, or call an MCP.

### Round 3 — Style (only when it matters)

If the user has their own design system, ask once for the file or link and match it. Otherwise default to the Anthropic palette below — no question needed.

Mobile-responsive defaults to yes for reports / explainers / decks; ask only if you're unsure.

### Recap (1 sentence, no question)

Before writing, restate the spec in one short line so the user can catch misreads:

> "OK — single HTML file, slide deck on prompt caching, 8 slides with presenter notes, Anthropic palette. Generating now."

### Generate and open in a Maestri portal

**Save location.** Write the file to a **temporary directory**, not the project directory — artifacts are throwaway by design (the whole point is that the *conversation* and the *exported state* are what you keep, not the HTML scaffolding). Use this path:

```bash
"${TMPDIR:-/tmp}/html-artifacts/<descriptive-name>.html"
```

Create the folder if it doesn't exist (`mkdir -p "${TMPDIR:-/tmp}/html-artifacts"`). Pick a descriptive filename (`onboarding-explorations.html`, not `output.html`); kebab-case, no spaces. If the user names a different path explicitly, honor that — but the default is always the tmp folder.

On macOS `$TMPDIR` resolves to something like `/var/folders/.../T/html-artifacts/`. The OS may clean it occasionally; if the user wants to keep an artifact, tell them to `cp` it somewhere durable or upload it to S3 (see Sharing below).

**Then open it in a Maestri portal**, which renders the file right next to the terminal on the user's canvas. The flow:

1. **First generation in this session** — create a fresh portal:
   ```bash
   maestri portal create "file:///absolute/path/to/file.html" "<short title>"
   ```
   Use a short, descriptive portal name (e.g. `"Onboarding explorations"`, `"PR #482 review"`).

2. **Iterating on an existing file** — ask the user once which they prefer for this session:
   - **Reuse the same portal** (overwrite in place):
     ```bash
     maestri portal edit "<portal name>" --url "file:///absolute/path/to/file.html"
     ```
     If they navigated away, `maestri portal navigate "<name>" "file://..."` works too. Tell them to hit reload in the portal toolbar if the page doesn't refresh.
   - **New portal each iteration** (compare versions side-by-side): write to a new filename with a `-v2`, `-v3` suffix and create a new portal each time.
   Once the user picks, stick with that choice for the rest of the session — don't keep asking.

The `file://` URL must be **absolute**. Resolve `~` and relative paths before passing them.

**Fallback.** Always try `maestri portal create` first. If `maestri` isn't on PATH (the terminal isn't running on a Maestri canvas), fall back to `open <file>` on macOS. Quick detection:
```bash
ARTIFACT="${TMPDIR:-/tmp}/html-artifacts/foo.html"
command -v maestri >/dev/null \
  && maestri portal create "file://$ARTIFACT" "Foo" \
  || open "$ARTIFACT"
```

After opening, ask if any tweaks are needed and iterate in-place.

## Quality bar

Every artifact this skill produces must:

- **Be a single file.** No external CSS, no external JS, no CDN. The user should be able to email it or upload it to S3 and have it just work. If you find yourself reaching for a CDN, write the few lines you need inline instead.
- **Open in any modern browser** with no build step. No bundlers, no preprocessors.
- **Use plausible content**, not "Lorem ipsum". If the user hasn't provided real data, generate realistic synthetic data (real-sounding names, credible numbers, code snippets in the right language for the user's project). Lorem makes the artifact feel like a demo; plausible content makes it feel like the real thing.
- **Be mobile-responsive** when the use case warrants it (reports, explainers, decks — yes; complex multi-pane editors — best effort).
- **Have an export button** if it's an editor or prototype. "Copy as markdown" / "Copy as JSON" / "Copy as prompt" — turn whatever the user did in the UI back into something pasteable into Claude Code. This is the whole point of throwaway editors: the user diverges into the UI, then converges back to text.

## Default design system

When the user hasn't specified visual style, default to this palette — calm, readable, works for almost every category:

```css
:root {
  --ivory:  #FAF9F5;  /* page background */
  --paper:  #FFFFFF;  /* cards / surfaces */
  --slate:  #141413;  /* headings, primary text */
  --clay:   #D97757;  /* primary accent (warm orange) */
  --clay-d: #B85C3E;  /* hover */
  --oat:    #E3DACC;  /* warm neutral fill */
  --olive:  #788C5D;  /* secondary accent (muted green) */
  --g100:   #F0EEE6;
  --g200:   #E6E3DA;
  --g300:   #D1CFC5;
  --g500:   #87867F;  /* muted text */
  --g700:   #3D3D3A;  /* body text */
  --serif:  ui-serif, Georgia, "Times New Roman", serif;
  --sans:   system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  --mono:   ui-monospace, "SF Mono", Menlo, Monaco, Consolas, monospace;
}
```

**Typography conventions:**
- Serif for big display headings (h1, sometimes h2). Slight negative letter-spacing on display sizes (`-0.018em`).
- Sans for body and small headings.
- Mono for code, timestamps, file paths, and small uppercase "eyebrow" labels above section headers.

**Layout conventions:**
- Single-column content wrapped at ~1120px on wide screens.
- Generous padding (32–48px sides on desktop, 16–24px on mobile).
- Border-radius: 8–12px for cards, 999px for pill-shaped nav.
- One `@media (max-width: 880px)` rule that collapses grids to single column is usually enough.

If the user has their own design system file (a CSS file, an HTML reference, or even a screenshot), ask for it once and use those tokens instead of the default.

## Patterns (copyable snippets)

**Slide deck — arrow-key navigation:**

```html
<section class="slide active">…</section>
<section class="slide">…</section>
<script>
  const slides = [...document.querySelectorAll('.slide')];
  let i = 0;
  addEventListener('keydown', e => {
    if (e.key === 'ArrowRight') i = Math.min(i + 1, slides.length - 1);
    if (e.key === 'ArrowLeft')  i = Math.max(i - 1, 0);
    slides.forEach((s, k) => s.classList.toggle('active', k === i));
  });
</script>
<style>.slide { display: none } .slide.active { display: block }</style>
```

**Copy-to-clipboard export:**

```js
async function exportAs(format) {
  const data = collectState();                // app-specific
  const text = format === 'json'
    ? JSON.stringify(data, null, 2)
    : toMarkdown(data);
  await navigator.clipboard.writeText(text);
  toast('Copied to clipboard');
}
```

**Tabbed sections without JS** (use checked radios + sibling selectors):

```html
<input type="radio" name="tab" id="t1" checked><label for="t1">Overview</label>
<input type="radio" name="tab" id="t2"><label for="t2">Details</label>
<div class="panel" data-for="t1">…</div>
<div class="panel" data-for="t2">…</div>
<style>
  .panel { display: none }
  #t1:checked ~ [data-for=t1],
  #t2:checked ~ [data-for=t2] { display: block }
</style>
```

**SVG diagrams:** always inline `<svg viewBox="0 0 W H">`. Don't `<img src="diagram.svg">` — inline SVG keeps everything in one file and stays editable. Use the design system colors via `currentColor` or direct hex.

## When to skip the discovery flow

Skip the conversation and write directly when:

- The user gave a fully-specified prompt (category + content + audience + style all clear in one message).
- The user asked for a tiny one-shot page ("make me a page with a button that says Hi").
- The user is mid-iteration on a file this skill already produced — just apply their edit.

For everything else, run the 2–3 question discovery. Thirty seconds of conversation upfront prevents a generic, unloved artifact.

## What this skill does NOT do

- **Doesn't write multi-file projects.** If the user genuinely needs a React app, a Next.js site, or a real frontend, suggest the `frontend-design` skill or a proper stack — don't try to cram one into a single HTML file.
- **Doesn't fetch live data.** Everything is static at generation time. For dynamic data, fetch it first with other tools (read files, run scripts, call MCPs, query git history) and bake the results into the HTML.
- **Doesn't create a generic "/html" workflow** for every prompt. The whole point is matching the artifact to the user's specific job-to-be-done. If you find yourself producing the same template twice in a row, the discovery flow is failing — ask one more question next time.

## Sharing

After generating, mention sharing options once (not every time the user iterates):

- "I've opened it in a portal next to the terminal."
- "To share with someone off the canvas: upload to S3 / Cloudflare R2 / any static host and send the link — it's a single self-contained file."

## Use-case cheat sheet

When the user names a category, here's what good looks like for each. Use these as a starting structure, not a rigid template.

**Most common for this user** — bias toward these when the request is ambiguous:

- **Implementation plan** — multi-section: overview, mockups, data flow SVG, code snippets, rollout phases, open questions. **Sidebar TOC** for navigating long pages. Anchor links between sections. This is the workhorse for any non-trivial coding task.
- **Standalone diagram (flowchart / dependency / sequence / mind map)** — a large inline `<svg viewBox="0 0 W H">` with labeled nodes and arrows, using design system colors. Optional click-to-reveal hotspots if there's depth. Short prose caption below explaining what the diagram says. See the SVG quick reference further down.

**Other categories:**

- **Exploration (compare options)** — single page with a grid of N variants, each labeled with the tradeoff it makes. Same scaffolding around each so differences pop. Short prose intro above the grid.
- **Code review / PR writeup** — diff rendered inline with margin annotations, color-coded by severity. Sections for context, diff, findings, next steps.
- **Explainer (feature or concept)** — TL;DR box at top, collapsible sections for the request path / lifecycle / etc., tabbed config snippets, FAQ or "gotchas" at the bottom. Glossary in the margin if there's jargon.
- **Status report** — header with the period, "shipped" list, "slipped" list, a small bar chart for velocity, a carryover section. Color-coded.
- **Incident report** — minute-by-minute vertical timeline on the left, log excerpts inline, follow-up checklist at the bottom.
- **Slide deck** — `<section class="slide">` per slide, arrow-key nav, optional presenter notes panel (toggleable).
- **Editor (drag/drop, form, tuning)** — interactive UI in the middle, instruction blurb at top, **prominent copy/export button at the bottom**.
- **Design system reference** — color tokens, type scale, spacing, components rendered as live examples next to their code.

## SVG diagram quick reference

For standalone diagrams, this scaffolding usually works:

```html
<svg viewBox="0 0 800 480" xmlns="http://www.w3.org/2000/svg" style="max-width:100%;height:auto">
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="6" markerHeight="6" orient="auto">
      <path d="M0,0 L10,5 L0,10 z" fill="#141413"/>
    </marker>
  </defs>

  <!-- nodes -->
  <rect x="40" y="60" width="160" height="64" rx="10" fill="#FFFFFF" stroke="#141413" stroke-width="1.5"/>
  <text x="120" y="98" text-anchor="middle" font-family="system-ui,sans-serif" font-size="14" fill="#141413">Source</text>

  <!-- edges -->
  <path d="M200,92 L320,92" stroke="#141413" stroke-width="1.5" fill="none" marker-end="url(#arrow)"/>
</svg>
```

Use design-system hex values directly in SVG attributes (CSS vars don't work inside `fill=` / `stroke=` attributes — only via inline `style=` or `<style>` blocks). For a diagram with the page CSS in scope, you can use `style="fill:var(--paper)"` instead.

Diagram type cheat-sheet:
- **Flowchart** — rounded rectangles (steps) + diamonds (decisions) + arrows.
- **Sequence** — vertical lifelines (dashed) with horizontal labeled arrows between them.
- **Dependency graph** — boxes with edges, no temporal order; consider grouping with `<g>` and a faint background `<rect>`.
- **Mind map** — center node with radial branches, color-coded by category.
- **Annotated timeline** — horizontal axis with dated tick marks, labels above and below.

## Implementation plan template

Structure for the workhorse "implementation plan" artifact:

```
┌─ Sidebar TOC ────────┬─ Main column ─────────────────────────┐
│ • Overview            │ # <Feature name>                       │
│ • Mockups             │ <one-line elevator pitch>              │
│ • Data flow           │                                        │
│ • Code touchpoints    │ ## Overview                            │
│ • Rollout             │ ## Mockups                             │
│ • Open questions      │ ## Data flow (inline SVG)              │
│                       │ ## Code touchpoints (snippets)         │
│                       │ ## Rollout phases (checklist)          │
│                       │ ## Open questions (clearly flagged)    │
└──────────────────────┴────────────────────────────────────────┘
```

The sidebar TOC should be `position: sticky; top: 0` on the left, ~220px wide. Each TOC entry is an anchor link (`<a href="#data-flow">`). Section headings on the right have matching `id`s.

Open questions deserve a visually distinct treatment (e.g. a clay-outlined box with a `?` icon) so reviewers can spot what still needs decisions.
