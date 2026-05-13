# SVG illustration sheet

A single page containing multiple labeled, downloadable SVG figures. Use this pattern when the user asks for *several* illustrations on a theme (e.g. "make me header illustrations for the docs section on background jobs", or a flowchart set, or icon variants).

Based on `10-svg-illustrations.html` in [thariqs/html-effectiveness](https://github.com/ThariqS/html-effectiveness).

## Structure

```
┌─ Sheet (narrow column, max 760px) ──────────┐
│ Header                                       │
│   H1 (serif)                                │
│   Lead paragraph (one short paragraph)       │
│   Meta line: "720 × 320 · inline SVG · …"   │
│                                              │
│ Figure A                                     │
│   .canvas        (border, rounded, ivory)   │
│     <svg>720×320 viewBox …</svg>            │
│   figcaption                                 │
│     left: title (serif) + subtitle (gray)   │
│     right: [Download SVG] button            │
│                                              │
│ Figure B  (same pattern)                     │
│ Figure C  (same pattern)                     │
│                                              │
│ Palette & rules                              │
│   swatches grid + bulleted rules            │
└──────────────────────────────────────────────┘
```

The page is narrow (~760px) because each figure is small enough to read inline. The palette + rules section at the bottom doubles as a style guide for any subsequent figures.

## The Anthropic illustration rules

These are the conventions that make a set of illustrations feel coherent. Bake them into every figure:

- **Aspect ratio**: 720×320 viewBox unless the user needs a different shape. Square (480×480) and tall (720×540) work too — but stay consistent across the sheet.
- **Background**: a `<rect>` covering the full viewBox in ivory (`#FAF9F5`). This makes the downloaded SVG self-contained.
- **Stroke widths**: 1.5px for neutral boxes/lines, 2px for emphasised containers (the "thing in focus" or a final state).
- **Corners**: all rectangles use `rx="10"`. No drop shadows, no gradients — flat fills only.
- **Color semantics**:
  - Clay (`#D97757`) marks "the thing in focus" — the head of a queue, the current step, the highlighted state.
  - Olive (`#788C5D`) marks "success / done".
  - Oat (`#E3DACC`) marks "transient holder" — workers, batchers, in-flight items.
  - Gray-150 (`#F0EEE6`) is the neutral fill for queued / pending items.
  - Slate (`#141413`) is the dark accent for primary text and a few high-emphasis fills (data stores in plan diagrams).
- **Typography inside SVGs**:
  - Labels inside boxes: 11px mono.
  - Annotations outside boxes (legends, captions, axis labels): 12px sans, gray-500.
  - Edge labels on arrows: 10.5px mono, gray-500 or clay matching the arrow color.

## Per-SVG `<style>` block

Each SVG should carry its own `<style>` inside `<defs>`. This lets the downloaded standalone .svg keep its fonts:

```html
<svg id="ill-queue" viewBox="0 0 720 320" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <style>
      .m { font-family: ui-monospace, "SF Mono", Menlo, Consolas, monospace; font-size: 11px; }
      .s { font-family: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif; font-size: 12px; }
    </style>
    <marker id="qa" markerWidth="10" markerHeight="10" refX="8" refY="4" orient="auto">
      <path d="M0,0 L0,8 L8,4 z" fill="#87867F"/>
    </marker>
  </defs>

  <!-- ivory background — must be inside the svg, not on a wrapper -->
  <rect width="720" height="320" fill="#FAF9F5"/>

  <!-- figure content here, using class="m" / class="s" for text -->
</svg>
```

## Page CSS

```html
<style>
  body { background: var(--ivory); color: var(--slate); font-family: var(--sans); padding: 64px 24px 96px; -webkit-font-smoothing: antialiased; }
  .sheet { max-width: 760px; margin: 0 auto; }

  header { margin-bottom: 56px; }
  header h1 { font-family: var(--serif); font-weight: 500; font-size: 34px; letter-spacing: -.01em; margin-bottom: 10px; }
  .lead { font-size: 14px; line-height: 1.6; color: var(--g700); max-width: 560px; }
  .meta { font-family: var(--mono); font-size: 11px; color: var(--g500); margin-top: 18px; }

  figure { margin: 0 0 72px 0; }
  .canvas { border: 1px solid var(--g300); border-radius: 12px; overflow: hidden; background: var(--ivory); }
  .canvas svg { display: block; width: 100%; height: auto; }

  figcaption { display: flex; align-items: baseline; justify-content: space-between; gap: 24px; padding: 18px 4px 0; }
  .cap-text { flex: 1; }
  .cap-title { font-family: var(--serif); font-size: 19px; font-weight: 500; margin-bottom: 4px; }
  .cap-sub { font-size: 13px; line-height: 1.55; color: var(--g500); }

  .dl { flex-shrink: 0; font-family: var(--mono); font-size: 11px; color: var(--slate); background: var(--g100); border: 1px solid var(--g300); border-radius: 6px; padding: 7px 12px; cursor: pointer; }
  .dl:hover { background: var(--oat); }
  .dl:active { transform: translateY(1px); }
</style>
```

## Download-SVG script

This is the script that wires each `[Download SVG]` button to extract the inline `<svg>` and save it as a standalone file. Include it once at the bottom of the page.

```html
<script>
  document.querySelectorAll('.dl').forEach(function (btn) {
    btn.addEventListener('click', function () {
      var svg  = document.getElementById(btn.dataset.target);
      var src  = new XMLSerializer().serializeToString(svg);
      var blob = new Blob([src], { type: 'image/svg+xml;charset=utf-8' });
      var url  = URL.createObjectURL(blob);
      var a    = document.createElement('a');
      a.href = url;
      a.download = btn.dataset.filename || 'illustration.svg';
      a.click();
      setTimeout(function () { URL.revokeObjectURL(url); }, 1000);
    });
  });
</script>
```

Markup for each figure's caption + button:

```html
<figure>
  <div class="canvas">
    <svg id="ill-queue" viewBox="0 0 720 320" xmlns="http://www.w3.org/2000/svg">…</svg>
  </div>
  <figcaption>
    <div class="cap-text">
      <div class="cap-title">Queue</div>
      <div class="cap-sub">For "How jobs are picked up" — intro page header.</div>
    </div>
    <button class="dl" data-target="ill-queue" data-filename="birchline-jobs-queue.svg">Download SVG</button>
  </figcaption>
</figure>
```

The `id` on the `<svg>` is the handle for `data-target`. Give every figure a unique id.

## Palette + rules footer

Include this section at the bottom so the artifact doubles as a style guide. Useful when the user comes back to ask for more illustrations later — they can paste a new figure into this sheet and stay on palette.

```html
<section class="palette">
  <h2>Palette &amp; rules</h2>

  <div class="swatches">
    <div class="swatch"><div class="swatch-chip" style="background:#FAF9F5"></div><div class="swatch-meta"><div class="swatch-name">ivory</div><div class="swatch-hex">#FAF9F5</div></div></div>
    <div class="swatch"><div class="swatch-chip" style="background:#141413"></div><div class="swatch-meta"><div class="swatch-name">slate</div><div class="swatch-hex">#141413</div></div></div>
    <div class="swatch"><div class="swatch-chip" style="background:#D97757"></div><div class="swatch-meta"><div class="swatch-name">clay</div><div class="swatch-hex">#D97757</div></div></div>
    <div class="swatch"><div class="swatch-chip" style="background:#788C5D"></div><div class="swatch-meta"><div class="swatch-name">olive</div><div class="swatch-hex">#788C5D</div></div></div>
    <div class="swatch"><div class="swatch-chip" style="background:#E3DACC"></div><div class="swatch-meta"><div class="swatch-name">oat</div><div class="swatch-hex">#E3DACC</div></div></div>
    <div class="swatch"><div class="swatch-chip" style="background:#F0EEE6"></div><div class="swatch-meta"><div class="swatch-name">gray-150</div><div class="swatch-hex">#F0EEE6</div></div></div>
    <div class="swatch"><div class="swatch-chip" style="background:#D1CFC5"></div><div class="swatch-meta"><div class="swatch-name">gray-300</div><div class="swatch-hex">#D1CFC5</div></div></div>
    <div class="swatch"><div class="swatch-chip" style="background:#87867F"></div><div class="swatch-meta"><div class="swatch-name">gray-500</div><div class="swatch-hex">#87867F</div></div></div>
  </div>

  <ul class="notes">
    <li>Strokes are 1.5px for neutral boxes, 2px for emphasised containers.</li>
    <li>All rectangles use <code>rx="10"</code>; no drop shadows or gradients.</li>
    <li>Labels inside boxes are 11px mono; annotations outside are 12px sans, gray-500.</li>
    <li>Clay marks "the thing in focus"; olive marks "success / done".</li>
    <li>Each SVG carries its own <code>&lt;style&gt;</code> block so the download stands alone.</li>
  </ul>
</section>
<style>
  .palette { border-top: 1px solid var(--g300); padding-top: 36px; margin-top: 8px; }
  .palette h2 { font-family: var(--serif); font-weight: 500; font-size: 19px; margin-bottom: 18px; }
  .swatches { display: grid; grid-template-columns: repeat(auto-fill, minmax(112px, 1fr)); gap: 10px; margin-bottom: 32px; }
  .swatch { border: 1px solid var(--g300); border-radius: 8px; overflow: hidden; background: var(--paper); }
  .swatch-chip { height: 44px; }
  .swatch-meta { padding: 8px 10px 10px; }
  .swatch-name { font-size: 12px; color: var(--g700); }
  .swatch-hex { font-family: var(--mono); font-size: 10px; color: var(--g500); }
  .notes { font-size: 13px; line-height: 1.7; color: var(--g700); list-style: none; }
  .notes li { padding-left: 18px; position: relative; margin-bottom: 6px; }
  .notes li::before { content: ""; position: absolute; left: 0; top: .65em; width: 8px; height: 1px; background: var(--g500); }
</style>
```

## Common figure recipes

A few SVG body recipes you'll reach for often:

**Queue / pipeline** — a row of equal-width boxes with the head box in clay; arrow to a worker container in oat on the right. Annotation below: "next to run".

**Retry with backoff** — a horizontal time axis, attempt markers (clay circles with X for fail, olive circle with check for success), dashed gray arcs looping over the gaps labeled `+1s`, `+2s`, `+4s`.

**Fan-out / fan-in** — a source box in oat on the left, four shard boxes in gray-150 in the middle, a merge box in olive on the right. Arrows: source → each shard (fan-out), each shard → merge (fan-in). Labels "fan-out" and "fan-in" at the top.

**State machine** — circles for states with mono labels inside, labeled arrows between them. Self-loops drawn as small arcs. Start state has a thicker stroke; terminal states are olive.

**Sequence diagram** — vertical dashed lifelines with a small labeled box at the top of each. Horizontal arrows between lifelines with mono labels above describing the message.
