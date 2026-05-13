# Implementation plan template

The workhorse format for any non-trivial coding plan artifact. Based on the layout from `16-implementation-plan.html` in [thariqs/html-effectiveness](https://github.com/ThariqS/html-effectiveness).

## Structure

```
┌─ Header ────────────────────────────────────────────────────┐
│ eyebrow (Implementation plan · <project>)                    │
│ H1 <Feature name>  (serif, ~38px)                           │
│ prompt-box: the prompt that generated this plan              │
├──────────────────────────────────────────────────────────────┤
│ Summary strip: 4-cell grid                                  │
│ [Effort] [Surfaces] [New tables] [Feature flag]             │
│ — one cell uses clay color for the headline number/word     │
├──────────────────────────────────────────────────────────────┤
│ 01 Milestones      — vertical dotted timeline               │
│ 02 Data flow       — inline SVG, request vs realtime arrows │
│ 03 Mockups         — 2-up grid of labeled UI sketches       │
│ 04 Key code        — 2-column dark code panels w/ filenames │
│ 05 Risks           — 3-col table w/ HIGH/MED/LOW pills      │
│ 06 Open questions  — clay-left-bordered cards w/ owner      │
└──────────────────────────────────────────────────────────────┘
```

Each section uses a numbered head:

```html
<div class="sec-head"><span class="num">01</span><h2>Milestones</h2></div>
<p class="sec-intro">…one-line framing in muted gray…</p>
```

Numbers sit in an oat-colored mono pill, h2 is serif. The `sec-intro` is optional but improves scanning.

For very long plans (>6 sections), add a sticky sidebar TOC on the left with anchor links and matching `id`s on each section. For ≤6 sections the numbered heads alone are enough — don't over-build.

## Component templates

### Header + prompt-box

```html
<header class="page-head">
  <div class="eyebrow">Implementation plan · Birchline web client</div>
  <h1>Comment threads on task cards</h1>
  <div class="prompt-box">
    <span class="label">Prompt</span>
    Create a thorough implementation plan for adding threaded comments to
    task cards. Include mockups, the data flow from client to persistence,
    the key code I'll need to write, and a risk table.
  </div>
</header>
<style>
  .page-head { margin-bottom: 48px; max-width: 820px; }
  .eyebrow { font-size: 12px; letter-spacing: .08em; text-transform: uppercase; color: var(--g500); margin-bottom: 12px; }
  .page-head h1 { font-family: var(--serif); font-weight: 500; font-size: 38px; line-height: 1.15; color: var(--slate); margin-bottom: 18px; letter-spacing: -.01em; }
  .prompt-box { background: var(--g100); border: 1.5px solid var(--g300); border-radius: 12px; padding: 16px 20px; font-size: 14.5px; color: var(--g700); }
  .prompt-box .label { font-family: var(--mono); font-size: 11px; text-transform: uppercase; letter-spacing: .06em; color: var(--g500); display: block; margin-bottom: 6px; }
</style>
```

Including the original prompt makes the artifact self-documenting — a year later you can read it back and remember exactly what you asked for.

### Summary strip

A 4-cell grid right under the header. Each cell has a small mono uppercase label and a big slate value. One value uses clay color to call out the most important fact (usually effort or a feature flag name).

```html
<div class="summary">
  <div class="cell"><div class="k">Effort</div><div class="v accent">~2 weeks</div></div>
  <div class="cell"><div class="k">Surfaces touched</div><div class="v">3 packages</div></div>
  <div class="cell"><div class="k">New tables</div><div class="v">2</div></div>
  <div class="cell"><div class="k">Feature flag</div><div class="v">task_comments_v1</div></div>
</div>
<style>
  .summary { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; margin-bottom: 64px; }
  @media (max-width: 900px) { .summary { grid-template-columns: repeat(2, 1fr); } }
  .summary .cell { background: var(--paper); border: 1.5px solid var(--g300); border-radius: 12px; padding: 18px 20px; }
  .summary .k { font-family: var(--mono); font-size: 11px; text-transform: uppercase; letter-spacing: .06em; color: var(--g500); margin-bottom: 6px; }
  .summary .v { font-size: 17px; color: var(--slate); font-weight: 600; }
  .summary .v.accent { color: var(--clay); }
</style>
```

### Numbered section head

```html
<style>
  section { margin-bottom: 64px; }
  .sec-head { display: flex; align-items: baseline; gap: 14px; margin-bottom: 8px; }
  .sec-head .num { font-family: var(--mono); font-size: 12px; background: var(--oat); color: var(--slate); padding: 3px 9px; border-radius: 8px; }
  .sec-head h2 { font-family: var(--serif); font-weight: 500; font-size: 26px; color: var(--slate); letter-spacing: -.01em; }
  .sec-intro { font-size: 14.5px; color: var(--g500); max-width: 720px; margin-bottom: 28px; }
</style>
```

### Milestone timeline

Vertical timeline where each item is `[date] [dot+line] [body]`. Done milestones use olive dots; upcoming use white dots with a clay ring.

```html
<div class="milestones">
  <div class="milestone">
    <div class="when">Week&nbsp;1 · Mon–Tue</div>
    <div class="dot-col"><span class="dot done"></span><span class="line"></span></div>
    <div class="body">
      <h3>Migration + read cursors</h3>
      <p>Two new tables, soft-delete, the read-cursor upsert.</p>
      <div class="tags"><span class="tag">packages/db</span><span class="tag">migration</span></div>
    </div>
  </div>
  <!-- more milestones; the last one's .line is hidden via :last-child rule -->
</div>
<style>
  .milestones { display: flex; flex-direction: column; }
  .milestone { display: grid; grid-template-columns: 120px 28px 1fr; gap: 0 18px; position: relative; }
  .milestone .when { text-align: right; font-family: var(--mono); font-size: 12px; color: var(--g500); padding-top: 4px; }
  .milestone .dot-col { display: flex; flex-direction: column; align-items: center; }
  .milestone .dot { width: 14px; height: 14px; border-radius: 50%; background: var(--paper); border: 3px solid var(--clay); margin-top: 4px; flex-shrink: 0; }
  .milestone .dot.done { background: var(--olive); border-color: var(--olive); }
  .milestone .line { width: 2px; flex: 1; background: var(--g300); margin: 4px 0; }
  .milestone:last-child .line { display: none; }
  .milestone .body { padding-bottom: 36px; }
  .milestone .body h3 { font-family: var(--serif); font-weight: 500; font-size: 19px; color: var(--slate); margin-bottom: 4px; }
  .milestone .body p { font-size: 14px; color: var(--g500); max-width: 620px; margin-bottom: 10px; }
  .milestone .tags { display: flex; gap: 8px; flex-wrap: wrap; }
  .milestone .tag { font-family: var(--mono); font-size: 11.5px; background: var(--g100); border: 1px solid var(--g300); border-radius: 6px; padding: 3px 8px; color: var(--g700); }
</style>
```

### Data flow diagram (the standout pattern)

A white card with an inline SVG that shows two distinct flows:

- **Solid gray arrows** — synchronous request/response paths. What the user waits on.
- **Dashed clay arrows** — realtime / async / fan-out paths. What happens after the response returns.

The caption below explains the legend in one sentence so the reader doesn't have to guess. Boxes are rounded white rectangles with a thin gray border. Data stores get a dark slate box with cream text. Edge labels are tiny mono text positioned next to each arrow describing what flows.

```html
<div class="diagram">
  <svg viewBox="0 0 860 340" xmlns="http://www.w3.org/2000/svg">
    <defs>
      <marker id="arrow" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse">
        <path d="M0,0 L10,5 L0,10 z" fill="#87867F"/>
      </marker>
      <marker id="arrowClay" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="7" markerHeight="7" orient="auto-start-reverse">
        <path d="M0,0 L10,5 L0,10 z" fill="#D97757"/>
      </marker>
    </defs>

    <!-- Service / component boxes (white) -->
    <g font-size="12" fill="#141413" font-family="ui-monospace,Menlo,monospace">
      <rect x="20" y="20" width="180" height="54" rx="10" fill="#FFFFFF" stroke="#D1CFC5" stroke-width="1.5"/>
      <text x="110" y="43" text-anchor="middle" font-weight="600">&lt;CommentComposer&gt;</text>
      <text x="110" y="60" text-anchor="middle" fill="#87867F" font-size="10.5">apps/web</text>

      <!-- Data store box (dark slate, cream secondary text) -->
      <rect x="340" y="266" width="180" height="54" rx="10" fill="#141413" stroke="#141413"/>
      <text x="430" y="289" text-anchor="middle" font-weight="600" fill="#FAF9F5">comments table</text>
      <text x="430" y="306" text-anchor="middle" fill="#C9B98A" font-size="10.5">postgres</text>
    </g>

    <!-- Solid gray arrows: synchronous request/response -->
    <g stroke="#87867F" stroke-width="1.5" fill="none">
      <path d="M110 74 L110 150" marker-end="url(#arrow)"/>
      <path d="M430 204 L430 266" marker-end="url(#arrow)"/>
    </g>

    <!-- Dashed clay arrows: realtime / async fan-out -->
    <g stroke="#D97757" stroke-width="1.5" fill="none" stroke-dasharray="5 4">
      <path d="M520 177 L660 177" marker-end="url(#arrowClay)"/>
      <path d="M660 162 C 540 120, 280 120, 205 162" marker-end="url(#arrowClay)"/>
    </g>

    <!-- Edge labels: tiny mono text describing what flows -->
    <g font-size="10.5" fill="#87867F" font-family="ui-monospace,Menlo,monospace">
      <text x="118" y="118">submit (id=temp)</text>
      <text x="548" y="172" fill="#D97757">broadcast row</text>
      <text x="360" y="112" fill="#D97757">reconcile temp id → real id</text>
    </g>
  </svg>
  <p class="caption">Solid = request/response path. Dashed clay = realtime fan-out. The composer never waits on the dashed path.</p>
</div>
<style>
  .diagram { background: var(--paper); border: 1.5px solid var(--g300); border-radius: 12px; padding: 28px; overflow-x: auto; }
  .diagram svg { display: block; min-width: 760px; }
  .diagram text { font-family: var(--mono); }
  .caption { font-size: 13px; color: var(--g500); margin-top: 12px; }
</style>
```

**Why it works.** The two arrow styles let the reader instantly separate "what the user waits on" from "what happens after the response." The caption seals it in one sentence. Hex values must be used directly in SVG `fill=`/`stroke=` attributes — CSS variables only work in `style=` or in a `<style>` inside `<defs>`.

### Mockups grid

Two-up grid of labeled containers, each showing a UI sketch in HTML. Use real-feeling content (real names, plausible task IDs, realistic copy) — never lorem.

```html
<div class="mocks">
  <div class="mock">
    <div class="mock-label">A · Thread inside an open task card</div>
    <div class="mock-body">…HTML mockup of the actual UI…</div>
  </div>
  <div class="mock">
    <div class="mock-label">B · Sidebar unread digest</div>
    <div class="mock-body">…</div>
  </div>
</div>
<style>
  .mocks { display: grid; grid-template-columns: 1fr 1fr; gap: 28px; }
  @media (max-width: 900px) { .mocks { grid-template-columns: 1fr; } }
  .mock { background: var(--paper); border: 1.5px solid var(--g300); border-radius: 12px; overflow: hidden; }
  .mock-label { padding: 12px 18px; border-bottom: 1.5px solid var(--g300); font-family: var(--mono); font-size: 11.5px; text-transform: uppercase; letter-spacing: .06em; color: var(--g500); background: var(--g100); }
  .mock-body { padding: 22px; }
</style>
```

### Key code panel

Two-column grid of dark code blocks (slate background, cream text). Each block has a mono filename label above. Apply minimal syntax highlighting with spans (`.kw` clay, `.str` olive, `.cm` muted, `.fn` warm yellow).

```html
<div class="code-grid">
  <div class="code-block">
    <div class="file-label">packages/db/migrations/0042_comments.sql</div>
    <div class="code"><pre><span class="kw">create table</span> <span class="fn">comments</span> (…)</pre></div>
  </div>
</div>
<style>
  .code-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 28px; }
  @media (max-width: 980px) { .code-grid { grid-template-columns: 1fr; } }
  .code-block { display: flex; flex-direction: column; gap: 10px; }
  .file-label { font-family: var(--mono); font-size: 12px; color: var(--g500); }
  .code { background: var(--slate); border-radius: 12px; padding: 18px 20px; overflow-x: auto; flex: 1; }
  .code pre { font-family: var(--mono); font-size: 12.5px; line-height: 1.65; color: #E8E6DE; white-space: pre; }
  .code .kw  { color: var(--clay); }
  .code .str { color: var(--olive); }
  .code .cm  { color: var(--g500); }
  .code .fn  { color: #C9B98A; }
</style>
```

### Risk table

Three columns: Risk | Severity | Mitigation. Severity uses a pill with semantic color (high = warm rose, med = oat, low = muted green). Don't put more than 5–6 rows — risks should be the ones that genuinely scare the team, not a brain-dump.

```html
<div class="risks">
  <div class="row"><div class="cell head">Risk</div><div class="cell head">Sev</div><div class="cell head">Mitigation</div></div>
  <div class="row">
    <div class="cell">Realtime duplicate: socket append races with the HTTP response.</div>
    <div class="cell"><span class="sev high">HIGH</span></div>
    <div class="cell">Dedupe on server-assigned id in the cache updater.</div>
  </div>
</div>
<style>
  .risks { border: 1.5px solid var(--g300); border-radius: 12px; overflow: hidden; background: var(--paper); }
  .risks .row { display: grid; grid-template-columns: 1.6fr 90px 1.6fr; }
  @media (max-width: 780px) { .risks .row { grid-template-columns: 1fr; } }
  .risks .row + .row { border-top: 1.5px solid var(--g300); }
  .risks .cell { padding: 14px 18px; font-size: 13.5px; }
  .risks .cell + .cell { border-left: 1.5px solid var(--g300); }
  @media (max-width: 780px) { .risks .cell + .cell { border-left: none; border-top: 1px dashed var(--g300); } }
  .risks .head { background: var(--g100); font-weight: 600; color: var(--slate); font-size: 12px; text-transform: uppercase; letter-spacing: .04em; }
  .sev { display: inline-block; font-family: var(--mono); font-size: 11px; padding: 2px 8px; border-radius: 6px; font-weight: 600; }
  .sev.high { background: #F3D9CC; color: #8A3B1E; }
  .sev.med  { background: var(--oat); color: var(--slate); }
  .sev.low  { background: #E4E9DC; color: #4B5C39; }
</style>
```

### Open questions

Clay-left-bordered cards that stand out from the rest of the page. Each card has the question, a short discussion, and an owner line ("Decide with · design, before slice 2"). These are the artifact's call-to-action — the reader's job is to answer them.

```html
<div class="open-q">
  <div class="q">
    <div class="qt">Do we allow editing, or only delete-and-repost?</div>
    <div class="qd">Editing needs an <code>edited_at</code> column. Leaning toward delete-only for v1.</div>
    <div class="owner">Decide with · design, before slice 2</div>
  </div>
</div>
<style>
  .open-q { display: flex; flex-direction: column; gap: 14px; max-width: 820px; }
  .q { background: var(--paper); border: 1.5px solid var(--g300); border-left: 4px solid var(--clay); border-radius: 10px; padding: 16px 20px; }
  .q .qt { font-weight: 600; font-size: 15px; color: var(--slate); margin-bottom: 4px; }
  .q .qd { font-size: 13.5px; color: var(--g500); }
  .q .owner { font-family: var(--mono); font-size: 11.5px; color: var(--g500); margin-top: 8px; }
</style>
```

## Page-level layout

Wrap everything in `.page { max-width: 1120px; margin: 0 auto; }`. Body padding `56px 32px 120px`. Use the default design system tokens from SKILL.md.
