# html-artifact

> 🌎 [Português (pt-br)](README.pt-br.md)

A [Claude Code](https://claude.com/claude-code) skill that creates rich, single-file HTML artifacts through a short discovery conversation — and opens them right next to your terminal in a [Maestri](https://www.themaestri.app/) portal.

Inspired by Thariq's ["Unreasonable Effectiveness of HTML"](https://x.com/trq212/status/2052809885763747935) and the [companion repo](https://github.com/ThariqS/html-effectiveness).

## What it does

Instead of taking a vague *"make me an HTML file"* and producing something generic, the skill runs a short conversation — at most 2–3 questions — to figure out what you actually want, then writes a self-contained `.html` file and opens it in a Maestri portal on your canvas (falling back to your default browser if Maestri isn't available).

The conversation is the product. The generation is the easy part.

## Why HTML over Markdown?

- **Information density** — tables, CSS layouts, inline SVG diagrams, embedded interactivity.
- **Easier to read** — a 200-line markdown spec gets skipped. The same content as HTML with a sidebar TOC gets read.
- **Easier to share** — single file, no build step, upload to S3 or send the file directly.
- **Two-way** — sliders, drag-drop editors, "copy as prompt" buttons let you stay in the loop with Claude.

## Installation

Clone into your user-level skills folder:

```bash
git clone git@github.com:RodriguesCosta/html-artifact-skill.git ~/.claude/skills/html-artifact
```

Restart Claude Code (or reload skills) and the skill becomes available.

## Usage

Just ask Claude Code anything that needs a rich one-off doc. The skill auto-triggers on:

- "Make me an HTML file / artifact"
- "Build me a throwaway editor for X"
- "Compare these 4 layouts side-by-side"
- "Write the implementation plan as HTML"
- "Show me this flow as an SVG diagram"
- "Generate a status report for the week"

The skill will ask you a couple of questions, recap the spec in one sentence, and write the file to a temporary directory (`$TMPDIR/html-artifacts/<descriptive-name>.html`). It then opens it in a Maestri portal next to your terminal.

### Categories supported

The skill knows the shape of these common artifact types:

- **Implementation plans** — header + summary strip + numbered sections (milestones, data flow diagram with dual-arrow style, mockups, key code, risks, open questions). Full template in [`references/implementation-plan.md`](references/implementation-plan.md).
- **SVG illustration sheets** — a set of labeled SVG figures with download buttons + palette/rules footer. Full template in [`references/svg-illustration-sheet.md`](references/svg-illustration-sheet.md).
- **Standalone SVG diagrams** — flowcharts, sequence, dependency graphs, mind maps
- **Explorations** — N variants side-by-side for picking a direction
- **Code reviews / PR writeups** with annotated diffs and findings by severity
- **Explainers** — TL;DR, collapsible sections, tabbed snippets, FAQ
- **Status & incident reports** with timelines and charts
- **Slide decks** with arrow-key navigation
- **Throwaway editors / prototypes** with prominent copy/export buttons
- **Design system references**

## Design system

By default the skill uses the calm Anthropic-style palette from Thariq's [html-effectiveness repo](https://github.com/ThariqS/html-effectiveness) — ivory background, clay (warm orange) and olive accents, serif display headings and sans-serif body. If you have your own design system, point Claude at it during the discovery and it'll match.

## Maestri integration

When running in a [Maestri](https://www.themaestri.app/) terminal, the skill opens generated HTML files in a portal on the canvas next to your terminal, so you can keep iterating without leaving the conversation.

- First generation creates a new portal.
- On subsequent edits, the skill asks once whether you want to reuse the same portal (overwrite in place) or open new portals per iteration (`-v2`, `-v3` for side-by-side comparison). Your choice sticks for the rest of the session.

If `maestri` isn't on your PATH, the skill falls back to `open <file>` on macOS.

## Where files are saved

By default in `$TMPDIR/html-artifacts/` (resolves to something like `/var/folders/.../T/html-artifacts/` on macOS). Artifacts are throwaway — keep what you exported (markdown / JSON / copied prompt), not the HTML scaffolding. If you want to keep one around, `cp` it somewhere durable or upload to S3. Pass an explicit path to override the default.

## License

MIT
