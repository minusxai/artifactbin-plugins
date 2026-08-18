---
name: "markup"
description: "artifact-bin markup (story JSX) authoring reference: component vocabulary, themes, and templates. Read BEFORE writing or editing any markup or markdown artifact for artifact-bin."
---

# artifact-bin markup reference

`markup` is THE document tier: **static JSX treated as data** — parsed,
validated, and interpreted over a fixed component registry; never executed.
It renders live with six switchable themes, a WYSIWYG human editor, and real
data-driven charts.

```
POST https://artifactbin.dev/api/artifacts
{ "title": "Q3 Board Report", "theme": "nocturne", "template": "editorial",
  "colorMode": "light", "markup": "<div data-design=\"tw\" className=\"@container p-8\">…</div>" }
```

Validation errors come back as `400 {"error":"invalid_jsx","details":[…]}`
with exact spans — fix and retry. (`markdown` is auto-converted to this
format at the door.)

## Ground rules

- **Static JSX only**: literal props (strings, numbers, booleans, arrays,
  `{{…}}` objects). No expressions, no spreads, no `<iframe>`, and no inline
  event handlers (`onClick=` is rejected) — the markup is data, never code.
- **But the document DOES run your JavaScript.** One `<script>` lives in the
  `<Helmet>` (below) and runs in the served document after it hydrates, so
  hand-written interactivity is ported, not rewritten: attach behaviour with
  `addEventListener` instead of inline handlers, and query your own ids and
  classes as usual.

## `<Helmet>` — the document's own head

At most ONE per document, holding at most one each of `<title>`, `<style>`
and `<script>`, plus `<meta name content />` pairs, plus the document's DATA
declarations — any number of `<Value>` and `<Query>` (see "Data" below).
Write it anywhere; it is hoisted to the top when stored. This is the ONLY
place a document may carry custom CSS or JS or declare data — a `<style>`,
`<title>`, `<Value>` or `<Query>` in the body is refused and told to come
here. (An `<svg>` keeps its OWN `<title>`: that is the graphic's
accessibility label, a different element sharing the name, and as many as you
have icons is fine.)

```jsx
<Helmet>
  <title>Quarterly review</title>
  <style>{`.rise { animation: rise .9s both } @keyframes rise { from { opacity: 0 } }`}</style>
  <script>{`
    document.getElementById('tab-2').addEventListener('click', function () {
      document.querySelectorAll('[data-panel]').forEach(function (p) { p.hidden = true; });
      document.getElementById('panel-2').hidden = false;
    });
  `}</script>
</Helmet>
```

Your script runs inside a sandboxed frame with an opaque origin: no network,
no cookies, no access to the surrounding page. `</script` cannot appear in
the text (split it: `'</scr' + 'ipt'`). It DOES get the document's data:
`window.mx` is defined before it runs — `mx.params.get('region')`,
`mx.params.set('region', 'EU')` (every dependent query re-runs and every
bound embed re-renders), `mx.params.subscribe(values => …)`,
`mx.data.get('sales')` (`{rows, columns}`), `mx.data.pending()` (the
queries a re-run has in flight — their previous rows stay in `get`
meanwhile), `mx.data.subscribe((state, pending) => …)`, `mx.refresh()`.
Plain values in and out; no React.
- **Style with Tailwind classes via `className`** — utilities carry layout,
  type, and spacing. Start the document with a
  `<div data-design="tw" className="@container …">` wrapper; use `@2xl:`
  container variants for responsive layout.
- **Custom CSS lives in the Helmet's `<style>` block, never inline**: for
  anything Tailwind cannot express (custom `@keyframes`, compound selectors,
  textures) put it in that one block, with the CSS wrapped in a template
  literal so its braces read as data — `<style>{`.rise { … }`}</style>` — and
  reference it by class. Inline `style=` attributes are rejected. Scope rules
  to your own class names (bare element selectors leak into chart/embed
  chrome), and take colors from theme tokens (`var(--primary)`,
  `var(--muted-foreground)`) so themes keep working. The cascade contract:
  **utilities compile `!important`** — a Tailwind class always beats your
  CSS, so never fight a utility from a style block. At save,
  `position: fixed/sticky` and external `url()`/`@import` are stripped, and
  `100vh` is rewritten to the reader-viewport variable.
- **Override a theme in CSS**: the selected theme is stamped on the iframe
  document root, and built-in colors, charts, radius, and fonts are variables.
  Put overrides in the Helmet's style block under `:root` — no theme-name
  selector, specificity trick, or `!important`:
  `<style>{`:root { --background: #0c0d0e; --primary: #ff6a1f; --chart-1: #ec6100; --font-display: Georgia, serif; }`}</style>`.
  Available palette keys are `--background`, `--foreground`,
  `--card`/`--card-foreground`, `--popover`/`--popover-foreground`,
  `--primary`/`--primary-foreground`, `--secondary`/`--secondary-foreground`,
  `--muted`/`--muted-foreground`, `--accent`/`--accent-foreground`,
  `--destructive`/`--destructive-foreground`, `--border`, `--input`,
  `--ring`, `--radius`, and `--chart-1..5`; type keys are
  `--font-body`, `--font-display`, and `--font-mono`.
- **Self-contained subresources**: `src`/`srcSet`/`poster` must be a
  `ref:<id>` or a `data:image/` URL — an external URL is rejected
  (`400 invalid_jsx`). Links (`href`) may point anywhere.
- **Theme tokens first**: colors like `text-muted-foreground`,
  `bg-muted`, `border-border`, `text-foreground`, `bg-background` follow the
  active theme; a page built on hardcoded palettes fights it. ONE bespoke
  accent via arbitrary values (`text-[#e2483d]`) is legitimate when the
  subject demands a hue no theme carries — spend it on the one bold moment,
  and know it will not follow a human's later theme switch.
- `theme` — a complete design system (fonts + full token palette). Pick by
  the subject's mood, then read https://artifactbin.dev/docs/themes/<name> for the chosen
  theme's full authoring guidance:
  - `modernist` — Stark Swiss editorial — white, near-black, one red accent; Archivo display over Inter, zero radius.
  - `classical` — Old-print, bookish — cream paper, sepia ink, oxblood accent; Cormorant Garamond display over Lora.
  - `nocturne` — Dark-first, technical — deep navy with violet accents; Inter throughout.
  - `organic` — Warm, soft, playful — sage green, terracotta, leafy chart tones; Fraunces display over Figtree, extra-round corners.
  - `broadsheet` — Newspaper/report — paper white, ink, steel blue; Source Serif 4.
  - `industry` — Professional, square — slate and industrial blue; Barlow Condensed display over Barlow.
- `template` (genre hint) — the document's structural genre, a REFERENCE not
  a contract: read https://artifactbin.dev/docs/templates/<name> for the chosen genre's beats,
  and deviate (or omit `template` entirely) when the subject suggests a
  better structure of its own. When the user's ask clearly names a genre
  (slides → `deck`, operating view → `dashboard`, board report → `editorial`),
  pick it; when it doesn't, **default to `scrolly`** — the designed, motif-led
  treatment is the better unspecified-case bet than a sober report. Genuinely
  torn between two readings of the ask? Clarify with the user, offering the
  candidates as options:
  - `editorial` — Typeset document/report — one centered text column paced in pages by a repeated folio rule, with numbered figures as the only wide elements.
  - `deck` — Presentation deck — full-viewport slides in acts; quiet paper slides between full-bleed accent dividers.
  - `scrolly` — Playful scrollytelling — a pudding.cool-style data story with a conceit, ticker bands, and chapter breaks.
  - `dashboard` — Operating view — a grid of draggable KPI and chart tiles with a one-line takeaway; almost no prose.
- `colorMode`: `light | dark`

## Data — declare in Helmet, bind by `$name`

A document DECLARES its data in `<Helmet>` and refers to it everywhere else
by name. One namespace; a name is either a TABLE (a `<Query>` or a table
`<Value>`) or a SCALAR (a `<Value>`), and every reference is checked
against that at publish — a typo is a `400` naming the token, never an
embed that renders empty.

```jsx
<Helmet>
  <Value name="region" type="string" />
  <Value name="min_rev" type="number" default={1000} />
  <Value name="tiny" type="table" value={[{"name":"John","age":34},{"name":"Mary","age":60}]} />
  <Query name="regions">{`select distinct region from ref_<datasetId> order by 1`}</Query>
  <Query name="sales">{`
    select region, sum(revenue) as revenue
    from ref_<datasetId>
    where ($region is null or region = $region) and revenue >= $min_rev
    group by 1 order by 2 desc
  `}</Query>
</Helmet>

<select value="$region" options="$regions" />
<input type="range" min={0} max={5000} value="$min_rev" />
<Question title="Revenue by region" data="$sales" viz={{"kind":"vega-lite","spec":{"mark":"bar","encoding":{"x":{"field":"region","type":"nominal"},"y":{"field":"revenue","type":"quantitative"}}}}} height="430px" />
<p>Total <Number data="$sales" col="revenue" agg="sum" prefix="$" format=",.0f" /></p>
<DataTable data="$sales" height="420px" columns={[{"col":"region","title":"Region"},{"col":"revenue","title":"Revenue","fmt":"$,.0f","bar":true}]} />
```

**Declarations (Helmet only)**

- `<Value name type default />` — a scalar the reader can change.
  `type`: `string | number | boolean | date` (default `string`);
  `default` must match it (dates are `YYYY-MM-DD`); no default = `null`,
  which is how "$region is null" in SQL means "all".
- `<Value name type="table" value={[{…}, …]} />` — an inline table (flat
  objects, non-empty; `columns={[{name,type}]}` optional). Read it in SQL by
  its bare name (`from tiny`) or bind it directly (`data="$tiny"`).
- `<Query name>{`select …`}</Query>` — SQL (DuckDB dialect) as a
  template-literal child, exactly one SELECT statement. A dataset artifact is
  the table `ref_<datasetId>`; another query or table Value is a table by
  its bare name (order does not matter; cycles are refused); a scalar Value
  is the bound parameter `$name` — never interpolated. Every `ref_<id>`
  must be a dataset YOUR token owns. The SQL is dry-run at publish against the
  real columns: a bad column is a `400 {"error":"invalid_sql"}` carrying the
  engine's own message with candidate names — read it and fix the query.
  Results are cut at 5,000 rows (`<DataTable>` reads the rest a window at a
  time); a query has 5 s.

**Bindings (body)**

- `<Question data="$table" viz={…} height="430px" />` — a chart over a
  declared table. The `viz` prop REQUIRES a `kind` discriminator:
  `{"kind":"vega-lite","spec":{…}}` for an inline spec (encoding fields are
  checked against the query's RESULT columns at publish),
  `{"kind":"recipe","recipe":"ref:<vizId>","bindings":{…}}` for a recipe
  artifact, `{"kind":"table"}` (the default when `viz` is absent) for a
  small themed table, and `{"kind":"single_value","yCols":["revenue"],
  "singleValueConfig":{"label":"Revenue","prefix":"$","format":",.0f"}}` for
  a KPI TILE (the column is summed over the table's rows — so point it at a
  one-row aggregate query; `format` is d3-format). `singleValueConfig`
  anywhere else is refused with this shape named.
- `<Number data="$table" col="revenue" agg="sum" prefix="$" format=",.0f" />`
  — one live aggregated figure, inline. NEVER type a figure into prose that the data can compute — <Number> inline instead; typed figures go stale and are often simply wrong.
  So write "revenue reached <Number … agg="sum" />", never "revenue reached 19400".
- `<DataTable data="$table" columns={[…]} sort={{"col":…,"dir":"desc"}} height="420px" />`
  — THE way to show many rows: virtualised, sortable by header, and honest
  about a cut result ("5,000 of 80,000", read more on scroll, sorted by the
  engine). `columns` picks and orders what shows: `{col, title, fmt
  (d3-format), align, bar: true (a proportional bar behind a number),
  colorScale: "sequential" | "diverging", width}`. Absent `columns` = every
  column of the table.
- Native controls bind scalars two-way — no component needed:
  `<select value="$region" options="$regions" />` (`options` = a table;
  column 1 is the value, column 2 the label if present; a null-default scalar
  gets an "All" entry), authored `<option>`s work too;
  `<input type="range|number|text|date" value="$x" />`;
  `<input type="checkbox" checked="$flag" />`; `<textarea value="$note" />`.
  A change writes the value (typed by the declaration), every query that
  binds `$x` re-runs, every embed over those queries re-renders. While a
  re-run is in flight the embed keeps its rows, dims, and shows an
  "updating…" chip (`aria-busy`); a query that fails shows the engine's
  message in place of the embed.
- `<Icon name="chart-bar" />` — a lucide icon, inline (kebab-case names from
  lucide.dev; unknown names render a question mark). Size it with a Tailwind
  `size-*` class; it inherits `currentColor`.
- `<img src="ref:<imageId>" />` — an uploaded image artifact (publish the
  image first: POST a base64 `data:` URL, or the raw bytes under a
  `Content-Type: image/<type>` header — see `/docs/llm`). A remote
  `https://` src is rejected; artifacts are self-contained.

`ref:<id>` survives ONLY for images and recipes. A dataset is read through a
`<Query>` — `data="ref:<id>"`, inline `data={[…]}` and `<Param>` are
retired and refused by name.

## Layout components

- `<SlideDeck><Slide title="…">…</Slide>…</SlideDeck>` — a presentation; each
  slide fills the viewport (the `deck` template). Hand-rolled sections work
  too: `h-screen` / `min-h-screen` resolve against the reader's viewport.
- `<Grid><GridItem x={0} y={0} w={6} h={3}>…</GridItem>…</Grid>` — the
  12-column dashboard canvas (the `dashboard` template); humans can drag
  tiles in the editor.

## Video

`<Video src="…" title="…" />` embeds a video player, 16:9 and full-width by
default (size it with `className`). `src` takes the link you would share —
a YouTube watch/short/embed URL, a Vimeo page, a Loom share — and is
normalized to the canonical embed player. Only these hosts render; anything
else shows an "unsupported source" notice. Raw `<iframe>` stays rejected —
`<Video>` is the only way to a nested frame.

## Motion (classes + your own CSS — no JS)

Think in moments, not effects: a page-load sequence for the hero, scroll
reveals for each section's evidence, hover micro-interactions on what is
interactive, at most one ambient loop for atmosphere. One orchestrated
moment lands harder than scattered effects. Everything fails open —
captures, exports, edit mode, and reduced-motion viewers always see the
finished, fully visible page.

- **Custom animation**: define `@keyframes` in your `<style>` block and
  attach them to classes — any easing, any choreography. Guard loops with
  `@media (prefers-reduced-motion: reduce)` in your CSS.
- **Scroll reveals, custom** (stories run no JS — the platform observer does
  the watching): stamp the element `data-reveal`, hide it under
  `:root[data-mx-motion] .your-class:not([data-mx-seen])`, and give it a
  transition to its natural state. The live viewer stamps `data-mx-seen`
  when the reader reaches it; the `data-mx-motion` root flag exists ONLY in
  the live view, which is what keeps captures and edit mode fully visible.
- **Kit shortcuts** (one class, no CSS needed):
  - `animate-marquee` — a real ticker: `overflow-hidden` band around
    `<div className="flex w-max animate-marquee">` whose content appears
    TWICE (two identical spans); speed via `[animation-duration:20s]`.
  - `reveal` `reveal-up` `reveal-left` `reveal-right` `reveal-scale` —
    prebuilt scroll reveals; stagger siblings with
    `[transition-delay:120ms]`, `[transition-delay:240ms]`.
  - `animate-fade-up` `animate-fade-in` `animate-scale-in` — hero load
    entrances, staggered with `[animation-delay:200ms]`.
  - `animate-float` (one ambient bob), `animate-caret-blink` (terminal caret).
- **Hover micro-interactions**: plain Tailwind — `transition
  hover:-translate-y-1`, `hover:bg-muted` — on cards and links.

## Inline SVG (subject motifs)

A minimal drawing subset renders inline for motifs and small diagrams — a
frame ruler, a route map, a sparkline decoration:

`<svg viewBox="0 0 640 48" className="w-full">` with
`g path line polyline polygon rect circle ellipse text tspan defs
linearGradient radialGradient stop clipPath title desc` (canonical camelCase
for `clipPath`/`linearGradient`/`radialGradient`). Use `currentColor` and
token-driven classes so the drawing follows the theme; gradients and clips
must reference LOCAL ids only (`fill="url(#g)"` — external `url(…)` targets
are rejected). No `use`/`image`/`foreignObject`/SMIL.

## Component vocabulary (the complete allowlist)

Kit components:

`Card` `CardHeader` `CardTitle` `CardDescription` `CardContent` `CardFooter` `CardAction` `Badge` `Button` `Alert` `AlertTitle` `AlertDescription` `Table` `TableHeader` `TableBody` `TableFooter` `TableRow` `TableHead` `TableCell` `TableCaption` `Separator` `Skeleton` `Progress` `Breadcrumb` `BreadcrumbList` `BreadcrumbItem` `BreadcrumbLink` `BreadcrumbPage` `BreadcrumbSeparator` `BreadcrumbEllipsis` `Avatar` `AvatarImage` `AvatarFallback` `AvatarBadge` `AvatarGroup` `AvatarGroupCount` `Tabs` `TabsList` `TabsTrigger` `TabsContent` `Accordion` `AccordionItem` `AccordionTrigger` `AccordionContent` `Collapsible` `CollapsibleTrigger` `CollapsibleContent` `Tooltip` `TooltipTrigger` `TooltipContent` `TooltipProvider` `Popover` `PopoverTrigger` `PopoverContent` `PopoverAnchor` `PopoverHeader` `PopoverTitle` `PopoverDescription` `Grid` `GridItem` `SlideDeck` `Slide` `Video` `Icon` `DataTable`

Plus the embeds `Question` `Number` (`DataTable` is in the kit list), the Helmet declarations `Value` `Query`, and these HTML tags:

`div` `span` `p` `h1` `h2` `h3` `h4` `h5` `h6` `ul` `ol` `li` `dl` `dt` `dd` `table` `thead` `tbody` `tfoot` `tr` `th` `td` `caption` `colgroup` `col` `a` `strong` `em` `b` `i` `u` `s` `code` `pre` `kbd` `samp` `var` `blockquote` `cite` `q` `abbr` `mark` `small` `sub` `sup` `del` `ins` `img` `figure` `figcaption` `picture` `source` `video` `audio` `track` `section` `article` `aside` `header` `footer` `main` `nav` `address` `hr` `br` `wbr` `time` `data` `details` `summary` `button` `input` `label` `select` `option` `optgroup` `textarea` `fieldset` `legend` `output` `meter` `progress` `datalist` `canvas` `dialog` `template` `svg` `g` `defs` `path` `line` `polyline` `polygon` `rect` `circle` `ellipse` `text` `tspan` `linearGradient` `radialGradient` `stop` `clipPath` `title` `desc`

Anything else is rejected by name with the allowed set echoed back.

## Skeleton (editorial)

```jsx
<Helmet><Query name="monthly">{`select month, sum(revenue) revenue from ref_<datasetId> group by 1 order by 1`}</Query></Helmet>
<div data-design="tw" className="@container px-6 py-12 @2xl:px-12 @2xl:py-16">
  <header className="max-w-4xl">
    <p className="animate-fade-in text-xs uppercase tracking-widest text-muted-foreground">Eyebrow</p>
    <h1 className="animate-fade-up mt-4 text-5xl @2xl:text-7xl font-bold tracking-tight leading-[1.05]">The headline states the finding</h1>
    <p className="animate-fade-up [animation-delay:200ms] mt-6 text-lg text-muted-foreground max-w-prose">The standfirst earns the scroll in one sentence.</p>
  </header>
  <section className="py-16">
    <h2 className="reveal-up text-2xl font-semibold tracking-tight">01 · A claim, never a topic</h2>
    <p className="mt-4 max-w-prose text-muted-foreground">Set up the chart — what to look at and why.</p>
    <div className="reveal-up mt-6"><Question title="Revenue by month" data="$monthly" viz={{"kind":"vega-lite","spec":{"mark":"line","encoding":{"x":{"field":"month","type":"temporal"},"y":{"field":"revenue","type":"quantitative"}}}}} height="430px" /></div>
  </section>
</div>
```

## Do / Don't

- DO cap body copy at `max-w-prose`; let charts break wider.
- DO put every number a viewer might question behind a `<Query>` and a
  `<Number>`/`<Question>`/`<DataTable>` — never hand-type a figure.
- DO spend motion deliberately: a hero entrance + section reveals, not
  every element animating.
- DON'T build the palette from hex — tokens follow the theme; one bespoke
  accent is the exception, fonts never are.
- DON'T reach for `html` because a component is missing — ask for the tag;
  most of HTML is already allowed.
- DON'T author in markdown: there is no markdown tier, and read-back is JSX and
  that is the round-trip format.

Read `https://artifactbin.dev/docs/llm` for auth, endpoints, versioning, and the MCP server;
`https://artifactbin.dev/docs/themes` and `https://artifactbin.dev/docs/templates` for the design and genre
references.


# artifact-bin themes

A `theme` is a complete design system — fonts plus the full token palette
(`--background`/`--foreground`/`--primary`/`--chart-1..5`/…) every kit
component and Tailwind token class follows. Set it as the top-level `theme`
field on a `markup`/`markdown` artifact; humans can switch it later in the
editor without recompiling.

Override the chosen theme with ordinary CSS in the markup's
`<Helmet><style>` block. The theme is on the iframe document root, so the supported
selector is simply `:root`:

```css
:root {
  --background: #0c0d0e;
  --primary: #ff6a1f;
  --chart-1: #ec6100;
  --font-display: Georgia, serif;
}
```

This repaints the actual page ground, recolors token classes and charts, and
retargets headings. It needs no theme-name selector and no `!important`.

Pick ONE by the subject's mood, then read its page for the full authoring
guidance — details about a theme you didn't pick are noise:

- `modernist` — Stark Swiss editorial — white, near-black, one red accent; Archivo display over Inter, zero radius. → https://artifactbin.dev/docs/themes/modernist
- `classical` — Old-print, bookish — cream paper, sepia ink, oxblood accent; Cormorant Garamond display over Lora. → https://artifactbin.dev/docs/themes/classical
- `nocturne` — Dark-first, technical — deep navy with violet accents; Inter throughout. → https://artifactbin.dev/docs/themes/nocturne
- `organic` — Warm, soft, playful — sage green, terracotta, leafy chart tones; Fraunces display over Figtree, extra-round corners. → https://artifactbin.dev/docs/themes/organic
- `broadsheet` — Newspaper/report — paper white, ink, steel blue; Source Serif 4. → https://artifactbin.dev/docs/themes/broadsheet
- `industry` — Professional, square — slate and industrial blue; Barlow Condensed display over Barlow. → https://artifactbin.dev/docs/themes/industry

Read `https://artifactbin.dev/docs/markup` for the component vocabulary and
`https://artifactbin.dev/docs/llm` for the publish API.

Full per-theme guidance ships with this skill: `themes/<name>.md` next to
this file — read the one you picked.

# artifact-bin templates

A `template` is the document's structural GENRE — its beat structure and
layout grammar — orthogonal to the design `theme`, which is purely a token
set. It is a REFERENCE, not a contract: each page documents a genre's beats
as a proven starting point, and a structure derived from the subject itself
beats any of them. Deviate deliberately, or omit the `template` field and go
bespoke — that is a first-class choice, not a fallback.

Choosing: when the ask clearly names a genre (slides → `deck`, operating
view → `dashboard`, board report / long-read → `editorial`), pick it. When
it is NOT obvious from the user's instructions, **default to `scrolly`** —
its conceit-led, designed treatment (at whatever register the subject can
carry, deadpan included) is the strongest default for an unspecified ask.
If you are genuinely torn between readings, clarify with the user and offer
the candidate genres as options rather than guessing.

Pick ONE by the content's shape, then read its page for the beats and layout
grammar — details about a genre you didn't pick are noise:

- `editorial` — Typeset document/report — one centered text column paced in pages by a repeated folio rule, with numbered figures as the only wide elements. → https://artifactbin.dev/docs/templates/editorial
- `deck` — Presentation deck — full-viewport slides in acts; quiet paper slides between full-bleed accent dividers. → https://artifactbin.dev/docs/templates/deck
- `scrolly` — Playful scrollytelling — a pudding.cool-style data story with a conceit, ticker bands, and chapter breaks. → https://artifactbin.dev/docs/templates/scrolly
- `dashboard` — Operating view — a grid of draggable KPI and chart tiles with a one-line takeaway; almost no prose. → https://artifactbin.dev/docs/templates/dashboard

Read `https://artifactbin.dev/docs/markup` for the component vocabulary and
`https://artifactbin.dev/docs/llm` for the publish API.

Full per-template guidance ships with this skill: `templates/<name>.md` next
to this file — read the one you picked.
