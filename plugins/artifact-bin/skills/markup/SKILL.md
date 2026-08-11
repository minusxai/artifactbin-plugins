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
  `{{…}}` objects). No expressions, no event handlers, no spreads, no
  `<script>`/`<iframe>`.
- **Style with Tailwind classes via `className`** — `<style>` blocks and the
  `style=` attribute are rejected. Start the document with a
  `<div data-design="tw" className="@container …">` wrapper; use `@2xl:`
  container variants for responsive layout.
- **Self-contained subresources**: `src`/`srcSet`/`poster` must be a
  `ref:<artifactId>` or a `data:image/` URL — an external URL is rejected
  (`400 invalid_jsx`). Links (`href`) may point anywhere.
- **Theme tokens over hex**: colors like `text-muted-foreground`,
  `bg-muted`, `border-border`, `text-foreground`, `bg-background` follow the
  active theme; hardcoded palettes fight it.
- `theme` — a complete design system (fonts + full token palette). Pick by
  the subject's mood, then read https://artifactbin.dev/docs/themes/<name> for the chosen
  theme's full authoring guidance:
  - `modernist` — Stark Swiss editorial — white, near-black, one red accent; Archivo display over Inter, zero radius.
  - `classical` — Old-print, bookish — cream paper, sepia ink, oxblood accent; Cormorant Garamond display over Lora.
  - `nocturne` — Dark-first, technical — deep navy with violet accents; Inter throughout.
  - `organic` — Warm, soft, playful — sage green, terracotta, leafy chart tones; Fraunces display over Figtree, extra-round corners.
  - `broadsheet` — Newspaper/report — paper white, ink, steel blue; Source Serif 4.
  - `industry` — Professional, square — slate and industrial blue; Barlow Condensed display over Barlow.
- `template` (genre hint) — the document's structural genre. Pick by the
  content's shape, then read https://artifactbin.dev/docs/templates/<name> for the chosen
  genre's beats and layout grammar:
  - `editorial` — Long-read feature or board report — chaptered argument with page breakers and a takeaways rail on every section.
  - `deck` — Presentation deck — full-viewport slides in acts; quiet paper slides between full-bleed accent dividers.
  - `scrolly` — Playful scrollytelling — a pudding.cool-style data story with a conceit, ticker bands, and chapter breaks.
  - `dashboard` — Operating view — a grid of draggable KPI and chart tiles with a one-line takeaway; almost no prose.
- `colorMode`: `light | dark`

## Data embeds (live, data-driven)

Create `dataset` / `viz` / `image` artifacts first, then bind them by id:

- `<Question title="Revenue by month" data="ref:<datasetId>" viz={{"kind":"vega-lite","spec":{"mark":"bar","encoding":{…}}}} height="430px" />`
  — a chart. The `viz` prop REQUIRES a `kind` discriminator:
  `{"kind":"vega-lite","spec":{…}}` for an inline spec,
  `{"kind":"recipe","recipe":"ref:<vizId>","bindings":{…}}` for a recipe
  artifact, `{"kind":"table"}` (the default when `viz` is absent) for a
  themed table.
- `<Number data="ref:<datasetId>" col="revenue" agg="sum" prefix="$" format=",.0f" />`
  — one live aggregated figure, inline. NEVER type a figure into prose that the data can compute — <Number> inline instead; typed figures go stale and are often simply wrong.
  So write "revenue reached <Number … agg="sum" />", never "revenue reached 19400".
- `<Param name="region" data={{"data":"ref:<datasetId>","column":"region"}} />`
  — a viewer-facing filter control; embeds with matching `filters` respond.
- `<img src="ref:<imageId>" />` — an uploaded image artifact.

Every `ref:<id>` must name an artifact YOUR token owns; bindings are
validated against the dataset's real columns at publish
(`400 {"error":"invalid_refs"}` on a miss).

## Layout components

- `<SlideDeck><Slide title="…">…</Slide>…</SlideDeck>` — a presentation; each
  slide fills the viewport (the `deck` template). Hand-rolled sections work
  too: `h-screen` / `min-h-screen` resolve against the reader's viewport.
- `<Grid><GridItem x={0} y={0} w={6} h={3}>…</GridItem>…</Grid>` — the
  12-column dashboard canvas (the `dashboard` template); humans can drag
  tiles in the editor.

## Component vocabulary (the complete allowlist)

Kit components:

`Card` `CardHeader` `CardTitle` `CardDescription` `CardContent` `CardFooter` `CardAction` `Badge` `Button` `Alert` `AlertTitle` `AlertDescription` `Table` `TableHeader` `TableBody` `TableFooter` `TableRow` `TableHead` `TableCell` `TableCaption` `Separator` `Skeleton` `Progress` `Breadcrumb` `BreadcrumbList` `BreadcrumbItem` `BreadcrumbLink` `BreadcrumbPage` `BreadcrumbSeparator` `BreadcrumbEllipsis` `Avatar` `AvatarImage` `AvatarFallback` `AvatarBadge` `AvatarGroup` `AvatarGroupCount` `Tabs` `TabsList` `TabsTrigger` `TabsContent` `Accordion` `AccordionItem` `AccordionTrigger` `AccordionContent` `Collapsible` `CollapsibleTrigger` `CollapsibleContent` `Tooltip` `TooltipTrigger` `TooltipContent` `TooltipProvider` `Popover` `PopoverTrigger` `PopoverContent` `PopoverAnchor` `PopoverHeader` `PopoverTitle` `PopoverDescription` `Grid` `GridItem` `SlideDeck` `Slide`

Plus the embeds `Question` `Param` `Number`, and these HTML tags:

`div` `span` `p` `h1` `h2` `h3` `h4` `h5` `h6` `ul` `ol` `li` `dl` `dt` `dd` `table` `thead` `tbody` `tfoot` `tr` `th` `td` `caption` `colgroup` `col` `a` `strong` `em` `b` `i` `u` `s` `code` `pre` `kbd` `samp` `var` `blockquote` `cite` `q` `abbr` `mark` `small` `sub` `sup` `del` `ins` `img` `figure` `figcaption` `picture` `source` `section` `article` `aside` `header` `footer` `main` `nav` `address` `hr` `br` `wbr` `time` `data` `details` `summary`

Anything else is rejected by name with the allowed set echoed back.

## Skeleton (editorial)

```jsx
<div data-design="tw" className="@container px-6 py-12 @2xl:px-12 @2xl:py-16">
  <header className="max-w-4xl">
    <p className="text-xs uppercase tracking-widest text-muted-foreground">Eyebrow</p>
    <h1 className="mt-4 text-5xl @2xl:text-7xl font-bold tracking-tight leading-[1.05]">The headline states the finding</h1>
    <p className="mt-6 text-lg text-muted-foreground max-w-prose">The standfirst earns the scroll in one sentence.</p>
  </header>
  <section className="py-16">
    <h2 className="text-2xl font-semibold tracking-tight">01 · A claim, never a topic</h2>
    <p className="mt-4 max-w-prose text-muted-foreground">Set up the chart — what to look at and why.</p>
    <div className="mt-6"><Question title="Revenue by month" data="ref:art_…" viz={{"kind":"vega-lite","spec":{"mark":"line","encoding":{"x":{"field":"month","type":"temporal"},"y":{"field":"revenue","type":"quantitative"}}}}} height="430px" /></div>
  </section>
</div>
```

## Do / Don't

- DO cap body copy at `max-w-prose`; let charts break wider.
- DO use `ref:` datasets for every number a viewer might question.
- DON'T hardcode hex colors, shadows, or fonts — the theme owns those.
- DON'T reach for `html` because a component is missing — ask for the tag;
  most of HTML is already allowed.
- DON'T re-send markdown after reading a document back: read-back is JSX and
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
set. Set it as the top-level `template` field on a `markup`/`markdown`
artifact and author the beats it prescribes.

Pick ONE by the content's shape, then read its page for the beats and layout
grammar — details about a genre you didn't pick are noise:

- `editorial` — Long-read feature or board report — chaptered argument with page breakers and a takeaways rail on every section. → https://artifactbin.dev/docs/templates/editorial
- `deck` — Presentation deck — full-viewport slides in acts; quiet paper slides between full-bleed accent dividers. → https://artifactbin.dev/docs/templates/deck
- `scrolly` — Playful scrollytelling — a pudding.cool-style data story with a conceit, ticker bands, and chapter breaks. → https://artifactbin.dev/docs/templates/scrolly
- `dashboard` — Operating view — a grid of draggable KPI and chart tiles with a one-line takeaway; almost no prose. → https://artifactbin.dev/docs/templates/dashboard

Read `https://artifactbin.dev/docs/markup` for the component vocabulary and
`https://artifactbin.dev/docs/llm` for the publish API.

Full per-template guidance ships with this skill: `templates/<name>.md` next
to this file — read the one you picked.
