---
name: "publish"
description: "Publish self-contained HTML artifacts (reports, dashboards, decks, datasets, charts, images) to artifact-bin and share the public link. Use when the user wants to publish, share, or host a page, report, dashboard, deck, dataset, chart, or image, or mentions artifact-bin."
---

With this plugin installed, prefer the artifact-bin MCP tools
(`create_artifact`, `update_artifact`, `edit_artifact`, `get_artifact`,
`list_artifacts`, `list_versions`, `get_version`, `revert_artifact`,
`delete_artifact`) — they speak the exact protocol below over the same
tokens, so the HTTP reference doubles as the tools' semantics (content
tiers, edit protocol, error meanings). Read the `markup` skill in this
plugin before authoring markup, and the `design` skill for design
fundamentals.

## Quick reference — everything a straightforward document needs

Publish with one call — the response carries the `id` and the `url` to hand over:

```bash
curl -X POST https://artifactbin.dev/api/artifacts \
  -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" \
  -d '{"title":"…","markup":"…","theme":"industry","template":"deck","colorMode":"dark"}'
```

200 answers `markup_changed`: false means the document stored exactly as you
sent it, true means storing rewrote it (formatting, a hoisted `<Helmet>`, a
`<p>` holding a block) and the canonical `markup` comes back — edit against
that. A 400 names exactly what to fix, so correct it and POST again. The deliverable is
`https://artifactbin.dev/a/<id>`. **`title` is what a browser tab and a shared link preview
show** — always set it; the on-page heading is not it.

**Editing a document you already published** — send the CHANGE, not the whole
file. `GET https://artifactbin.dev/api/artifacts/<id>` returns the current `markup` and an
`edit_id`; pass that `edit_id` back with the exact text to swap:

```bash
curl -X POST https://artifactbin.dev/api/artifacts/<id>/edits \
  -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" \
  -d '{"edit_id":"…","old_string":"exact text once in the document","new_string":"replacement"}'
```

`old_string` must appear EXACTLY ONCE. Prefer this over re-PUTting the whole
document: it is smaller, and a human may be editing the same page live.

**markup** is JSX treated as data: ordinary HTML tags for everything including
prose (`h1 h2 p ul li blockquote table figure img`, inline `svg`) plus the
component kit (`Card`, `Tabs`, `Badge`, `Grid`/`GridItem`,
`SlideDeck`/`Slide`, `Icon`, and the data embeds `Question`,
`DataTable`, `Number`), styled ONLY with Tailwind utilities via
`className` — inline `style=` is rejected. There is no markdown.

**Guess rather than look up.** A tag or component outside the allowlist is
refused with a 400 carrying the whole allowed set (`allowed_html_tags`), so a
wrong guess costs one cheap round trip — where fetching the full component list
costs far more, every turn, for the rest of the run. Custom CSS and JS live in at most one `<Helmet>`, which also holds
the `<title>`:

```jsx
<Helmet>
  <title>What the tab shows</title>
  <style>{`:root { --primary: #ff6a1f; }`}</style>
</Helmet>
```

**A document is a CONTAINER, and a reader may be on a phone (390px).** Use
container prefixes — `@2xl:`, `@3xl:` — never the viewport ones
(`sm:`/`md:`/`lg:`), which do not apply here. Multi-column layouts start at
one column and widen: `grid-cols-1 @2xl:grid-cols-3`, and so does display
type — `text-4xl @2xl:text-6xl`, never a bare `text-6xl` (60px type and one
long proper noun is wider than a phone). Never a fixed pixel width.

Rules a document lives by: one self-contained document; no CDN scripts, external
stylesheets or network calls (it is served sandboxed and they silently fail);
images are a `data:` URI or an `https://` URL (imported and stored at publish);
web fonts by naming a Google family — `<meta name="font-display" content="Lobster" />`.

**theme** (the whole palette and fonts — author with token classes like
`bg-background`, `text-muted-foreground`, `bg-primary`, `border-border`
and it follows):

- `modernist` — Stark Swiss editorial — white and near-black with one red accent, inverted to ink-black in dark
- `organic` — Warm, soft, playful — sage green, terracotta, leafy chart tones
- `industry` — Professional, square — slate and industrial blue, safety-orange for the one exception
- `terminal` — Terminal — mono type throughout, near-black with neon green by default
- `manuscript` — Serif editorial — cream paper, sepia ink, oxblood accent
- `pop` — Playful and loud — candy magenta, cyan and amber over near-white, chunky radii, heavy Bricolage Grotesque headings

`colorMode`: `light` | `dark`.

**template** — the structural genre. Pick by the ask (slides → deck, operating
view → dashboard, report/long-read → editorial); when it is not obvious,
`scrolly` is the strongest default. Deviating deliberately is first-class.

- `deck` — `<SlideDeck>` wrapping one `<Slide title="…">` per slide (each
  fills the viewport as a flex column; never vh units, never fixed/sticky).
  Every content slide repeats ONE chrome: kicker
  `<p className="text-xs uppercase tracking-widest font-semibold text-primary">01 · ACT</p>`,
  `<hr className="mt-3 mb-10"/>`, a spoken-sentence `<h2>` claim, two lines of
  copy or ≤3 bullets, then `<div className="mt-auto pt-10 flex justify-between text-xs text-muted-foreground">`.
  At least ONE full-bleed accent divider slide (`className="justify-center bg-primary text-primary-foreground"`,
  a giant tone-on-tone numeral, no band or footer) — everything else stays quiet paper.
  ~40% empty, one idea per slide.
- `editorial` — ONE centered column, `<article className="mx-auto max-w-2xl">`.
  Numbered section claims as `<h2>`s (3+ get an automatic table of contents),
  measured prose, a repeated folio rule opening each section. Charts may widen
  symmetrically (`@3xl:-mx-24`); tables never do.
- `dashboard` — `<Grid>`/`<GridItem>` tiles wall to wall, almost no prose: a
  quiet one-line verdict (`text-base font-medium`, never a display headline),
  a control row, KPI numbers as the only big type, tile labels ≤3 words.
- `scrolly` — a conceit the whole page commits to (a console, a case file),
  mono uppercase tracked labels (`font-mono text-xs uppercase tracking-widest`),
  huge two-tone claims, chapter bands, `reveal-up` on evidence as it arrives.

Checking your work: a 200 has already validated the document. `GET https://artifactbin.dev/a/<id>/export`
renders it as a PNG (add `?slide=2` for one slide of a deck, 1-based — the whole-deck
shot is every slide stacked and too small to read) — fetch it only if you can actually
view images; otherwise read the stored markup back instead.

**Does the document read DATA?** A dashboard, a chart over a dataset, anything
with a `<Query>` or a `<Question>` — that vocabulary is NOT above, and
guessing it wastes far more than reading does. Read https://artifactbin.dev/docs/llm BEFORE you
write — the data path is at the TOP of that page, in the first screenful, so a
partial read is enough; do not skim past it. It is also the only API
description there is (no OpenAPI or Swagger endpoint to find).

**A document of prose, slides or sections is fully covered above** — it costs
more tokens to read the full docs than to write one, so writing straight from
this sheet is the normal path and usually the whole job.

When you DO need more, the answer is one fetch away and you should take it —
https://artifactbin.dev/docs/llm (the whole API) and https://artifactbin.dev/docs/markup (every component).
That covers data and `<Query>`, live reader controls, `<Mutation>` writes,
image and chart artifacts, versions and partial edits. Fetching one known page
is always cheaper than guessing at endpoints: there is no OpenAPI, no Swagger
and no `/api/docs` to find, so a guess is a 404 and the page is not.


## The rest of the protocol

Everything above covers a straightforward document. For the parts it does not
— datasets and `<Query>` in full, the `/edits` splice protocol, annotations,
versions and revert, visibility and sharing, the error table — fetch
`https://artifactbin.dev/docs/llm`; the essentials are at the top of that page and it names
every section. `https://artifactbin.dev/docs/markup` is the authoring vocabulary, and the
`markup` skill beside this one carries it with every theme and template.