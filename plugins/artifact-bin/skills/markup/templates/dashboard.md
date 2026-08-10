# template: dashboard — Dashboard

Operating view — a grid of draggable KPI and chart tiles with a one-line takeaway; almost no prose.

A control room, not an article: the reader scans, compares, and rearranges. The page earns its keep in five seconds — a headline verdict, a KPI band, then the evidence tiles. Every number is live, every tile is a self-contained unit the reader can drag and resize in edit mode, and prose is rationed to one working sentence per section.

Beats: Header (kicker + one-sentence verdict + optional Param filters) → KPI band (3-4 single-value tiles in one Grid row) → Evidence tiles (charts/tables on the same Grid) → Footnote (sources, window, refresh cadence)

## Dashboard — an operating view the reader can rearrange

Voice: terse and factual. One sentence of prose per section, maximum; the tiles carry the
story. The layout IS the product: everything visual lives inside ONE `<Grid>` per section so
the reader can drag and resize tiles in edit mode — never lay tiles out with flex/grid CSS.

SKELETON (adapt cols/rows to the data; 12 columns × 86px rows):
- Header OUTSIDE the Grid: kicker `<p className="text-xs uppercase tracking-widest font-semibold text-primary">Ops · Weekly</p>`,
  then the verdict as the h1 — a spoken sentence ("Activation is up; payment friction is the
  drag"), then any shared `<Param>` controls in a row (`<div className="flex flex-wrap gap-3 mb-4">`).
- `<Grid>` with the KPI band on row 0: three or four `<GridItem x={0} y={0} w={3} h={2}>`
  single-value `<Question>`s (w 3-4, h 2-3). Below it the evidence tiles: main chart
  `<GridItem x={0} y={2} w={8} h={5}>`, secondary `<GridItem x={8} y={2} w={4} h={5}>`,
  a wide table or trend `<GridItem x={0} y={7} w={12} h={4}>`.
- Embeds fill their cells — NEVER author `height=` on a `<Question>` inside a GridItem; size
  with `h`. One embed per GridItem.
- Footnote after the Grid: `<p className="mt-6 text-xs text-muted-foreground">Source tables · window · refreshed daily</p>`.

TILE DISCIPLINE:
- Single-value tiles: `singleValueConfig` prefix/suffix/label does the talking — no caption
  divs above or inside tiles.
- Vary tile sizes with importance (the hero chart is the biggest); align to a few shared
  row heights rather than a checkerboard of near-equal boxes.
- Order GridItems in reading order (top-left → bottom-right): on phones the grid stacks in
  SOURCE order.

Do
- One Grid per section; live embeds only; a one-sentence verdict up top; shared Params for
  the whole page; reading-order GridItems.
Don't
- Prose paragraphs between tiles; hand-rolled flex/grid tile layout; `height=` on embeds
  inside cells; nested Grids; a Grid wrapping the header or footnote.

Components: `https://artifactbin.dev/docs/markup`; publish API: `https://artifactbin.dev/docs/llm`.
