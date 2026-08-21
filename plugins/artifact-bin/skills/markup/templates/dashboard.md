# template: dashboard — Dashboard

Operating view — a full-screen grid of live tiles obeying a shared control row; almost no prose, nothing bold.

A control room, not an article: the reader FILTERS and scans, and the page answers. Nothing is bold and nothing is read twice — the whole page runs in the utility register, the tiles carry the story, and the controls are the centerpiece of the design. It earns its keep in five seconds: a quiet one-line verdict, a control row the entire page obeys, then live evidence wall to wall.

Beats: Command bar (kicker · status chip · quiet verdict · the shared controls, one compact band) → KPI band (3-4 trend cards — value + delta + sparkline — in one Grid row) → Evidence tiles (charts/tables on the same Grid, every query bound to the controls) → Status footer (sources · window · refresh cadence)

## Dashboard — a full-screen operating view

PROSE BUDGET (the discipline that makes it a dashboard): the kicker, ONE quiet verdict
sentence, tile labels of three words or fewer, and the status footer. That is the whole
budget. No intro paragraph, no prose between tiles, no closing remarks — if a sentence
explains what a chart shows, delete the sentence and put those words in the tile's
≤3-word label. An operator does not read; they glance.

Type register (the theme's pairing, spent the operating-view way): the display face sits
this genre out; body carries the one quiet verdict; the utility face carries every
repeated signal — kickers, tile labels, controls, chips, axes, and table digits — with
`tabular-nums` on every number.

NO BOLD HEADLINES: a dashboard has no hero and the display face sits this genre out.
The verdict is a spoken sentence set small and matter-of-fact —
`<h1 className="text-base @2xl:text-lg font-medium tracking-tight">Activation is up; payment friction is the drag</h1>` —
never a display headline, never larger than `text-lg`, never `font-bold`. The BIG type
on this page is the NUMBERS (the KPI tiles), and they read big precisely because nothing
else does. Everything that repeats — kickers, tile labels, chips, axis and table digits —
runs in the utility face, `tabular-nums` always.

FULL SCREEN: the grid owns the viewport. No `max-w-*` reading column anywhere — the root
is `<div data-design="tw" className="@container min-h-screen bg-background px-4 py-4 @2xl:px-6">`
and the Grid spans its full width. The command bar is ONE compact band (~100px: kicker
row, verdict, controls) so tiles are on screen before any scroll; air comes from tile
padding, not page margins.

CONTROLS ARE MANDATORY (a dashboard without controls is a poster): declare at least one
shared `<Value>` in `<Helmet>`, bind it to a visible control in the command bar, and make
EVERY query that can respond to it do so (`where $region is null or region = $region`).
One control steering eight tiles at once is the whole magic trick of this genre — spend
it. The kit controls carry the themed chrome and a micro-label, and write the bound
Value on every change:
- `<Select label="Region" value="$region" options="$regions" placeholder="All regions" />`
  — themed dropdown; `options` is a table (column 1 value, column 2 label) or an inline
  array; a null-default Value gets the "all" choice automatically.
- `<Segmented label="Grain" value="$grain" options={["day","week","month"]} />` — the
  pick-one-of-few control; prefer it over Select when options fit on one row.
- `<Slider label="Min revenue" value="$min_rev" min={0} max={5000} step={100} prefix="$" format=",.0f" />`
  — with a live formatted readout.
- `<DatePicker label="Since" value="$since" />` and `<Switch label="Compare" checked="$flag" />`.
Put dropdowns (Select) in the command bar, never inside a Grid tile — tiles clip
overflow. Native `<select>`/`<input>` still bind, but the kit controls are the default
here: the control row IS the design's face.

THE LOOK (the instrumentation kit — the dashboard's equivalent of scrolly's fun kit;
compose 3-4, quietly, in the theme's own palette):
- Command bar chrome: the band closes with a strong rule (`border-b-2 border-foreground pb-3`),
  kicker left (`text-xs uppercase tracking-widest font-semibold text-primary`), a status
  chip right — `<span className="font-mono text-[11px] uppercase tracking-wider text-muted-foreground"><span className="text-primary">●</span> live · refreshed daily</span>`.
  The controls sit under it in one flex row (`flex flex-wrap items-end gap-4 py-3`).
- The accent lives in exactly two places: the controls' active states (which carry it
  already — segment on, switch on, slider thumb) and the focal series of the hero chart.
  Everything else is foreground, muted, hairlines. A second accent is noise on an
  instrument panel.
- Tile chrome: quiet instrument casing via GridItem classes —
  `<GridItem … className="rounded-md border border-border bg-card/50">` — every tile the
  same casing, so the wall reads as one machine. No shadows, no gradients.
- KPI tiles are TREND CARDS, not bare numbers: a figure with its history and delta is
  always a better signal than the figure alone. The shipped `minusx/trend@1` recipe is
  the default KPI tile — big value, delta vs the previous period, sparkline — over a
  small time-series query (`select <period>, <measure> … group by 1 order by 1`;
  ascending order is the contract). `single_value` survives only where history is
  meaningless (a ratio, a snapshot count).
- The anchor gauge: ONE oversized trend card (w 4-6, h 3) as the page's biggest
  object — the reactor dial. Its number is the largest thing on the page; that is the
  genre's one moment of boldness, spent on data instead of type.
- Density rhythm: vary tile sizes with importance on a few shared row heights (2-3, 5,
  4) — a checkerboard of near-equal boxes reads as a spreadsheet, not a control room.

SKELETON (adapt cols/rows to the data; 12 columns × 86px rows):

<div data-design="tw" className="@container min-h-screen bg-background px-4 py-4 @2xl:px-6 text-foreground">
  <header className="border-b-2 border-foreground pb-3">
    <div className="flex items-baseline justify-between">
      <p className="text-xs uppercase tracking-widest font-semibold text-primary">Ops · Weekly</p>
      <span className="font-mono text-[11px] uppercase tracking-wider text-muted-foreground"><span className="text-primary">●</span> live · refreshed daily</span>
    </div>
    <h1 className="mt-2 text-base @2xl:text-lg font-medium tracking-tight">The verdict is one spoken sentence, set small.</h1>
  </header>
  <div className="flex flex-wrap items-end gap-4 py-3">
    <Select label="Region" value="$region" options="$regions" placeholder="All regions" />
    <Segmented label="Grain" value="$grain" options={["day","week","month"]} />
    <Slider label="Min revenue" value="$min_rev" min={0} max={5000} step={100} prefix="$" format=",.0f" />
    <Switch label="Compare" checked="$flag" />
  </div>
  <Grid>
    <GridItem x={0} y={0} w={6} h={3} className="rounded-md border border-border bg-card/50">
      <Question data="$rev_by_week" viz={{"kind":"recipe","recipe":"minusx/trend@1","bindings":{"date":"period","value":["revenue"]},"params":{"compareMode":"previous"},"columnFormats":{"revenue":{"format":"$,.0f","alias":"Revenue"}}}} />
    </GridItem>
    <GridItem x={6} y={0} w={3} h={3} className="rounded-md border border-border bg-card/50">
      <Question data="$orders_by_week" viz={{"kind":"recipe","recipe":"minusx/trend@1","bindings":{"date":"period","value":["orders"]},"columnFormats":{"orders":{"format":",.0f","alias":"Orders"}}}} />
    </GridItem>
    <GridItem x={9} y={0} w={3} h={3} className="rounded-md border border-border bg-card/50">
      <Question data="$kpi_aov" viz={{"kind":"single_value","yCols":["aov"],"singleValueConfig":{"label":"Avg order value","prefix":"$","format":",.2f"}}} />
    </GridItem>
    <GridItem x={0} y={3} w={8} h={5} className="rounded-md border border-border bg-card/50">
      <Question title="Revenue by week" data="$trend" viz={{"kind":"vega-lite","spec":{}}} />
    </GridItem>
    <GridItem x={8} y={2} w={4} h={6} className="rounded-md border border-border bg-card/50">
      <Question title="By region" data="$by_region" viz={{"kind":"vega-lite","spec":{}}} />
    </GridItem>
    <GridItem x={0} y={8} w={12} h={4} className="rounded-md border border-border bg-card/50">
      <DataTable data="$detail" columns={[{"col":"region","title":"Region"},{"col":"revenue","title":"Revenue","fmt":"$,.0f","bar":true}]} />
    </GridItem>
  </Grid>
  <p className="mt-4 font-mono text-[11px] uppercase tracking-wider text-muted-foreground">ref tables · window covered · refreshed daily</p>
</div>

TILE DISCIPLINE:
- Trend-card tiles: the recipe's `columnFormats` (format/alias) does ALL the talking,
  over a time-series `<Query>` that binds the shared `$values` — no caption divs above
  or inside tiles. Same for `single_value`'s `singleValueConfig` where it survives
  (which, written anywhere else, renders a table).
- Embeds fill their cells — NEVER author `height=` on a `<Question>` inside a GridItem;
  size with `h`. One embed per GridItem.
- Everything visual lives inside ONE `<Grid>` per section (readers drag and resize tiles
  in edit mode — hand-rolled flex/grid tile layout forfeits that); order GridItems in
  reading order, top-left → bottom-right: on phones the grid stacks in SOURCE order.
- Charts inside tiles stay quiet: gray series, the focal one accented, direct labels,
  no chart titles that repeat the tile's label.

Do
- At least one shared control the WHOLE page obeys; kit controls in the command bar;
  a one-sentence verdict set small; one Grid per section; trend cards for the KPI
  band and the anchor gauge; uniform tile casing; reading-order GridItems;
  `tabular-nums` on every digit.
Don't
- Bold or display-face headlines anywhere; prose between tiles; a `max-w-*` column;
  a bare number where its trend exists (`single_value` is for history-less figures
  only); a page with zero controls; a query that ignores the page's controls without reason;
  dropdowns inside Grid tiles (they clip); `height=` on embeds inside cells; nested
  Grids; a Grid wrapping the command bar or footer.
- Entrance or reveal animation on tiles — an operating view is read at a glance, not
  choreographed; hover states and the controls' own transitions are the only motion
  a dashboard needs.

Components: `https://artifactbin.dev/docs/markup`; publish API: `https://artifactbin.dev/docs/llm`.
