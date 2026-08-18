# template: editorial — Editorial

Typeset document/report — one centered text column paced in pages by a repeated folio rule, with numbered figures as the only wide elements.

A printed document rendered on screen: a white paper, a memo to the board, a report you could hand across a desk. One quiet centered column of measured prose, paced in PAGES — every section opens under the same folio rule as if a fresh sheet were fed in — and numbered figures (Vega charts, drawn SVG diagrams, images) do the showing. Minimal is the register: hairlines, small caps, and numbers; the restraint is what reads as authority.

Beats: Title block (masthead rule · title · standfirst · byline/date) → Summary (hairline-bounded, bold-lead findings) → Numbered sections (3-6, one claim each, each opening on a fresh page and closing on a takeaway) → Figures throughout (FIG-numbered — chart, diagram, or image) → Closing section (the so-what) + method footer

## Editorial — the typeset document / report

Voice: a document, not a webpage. Section headlines are numbered CLAIMS in sentence case
("2. Retention paid for the price increase"), set quiet (`font-semibold tracking-tight`)
— reading only the headlines delivers the whole argument. The body is measured prose:
short paragraphs, no dangling one-line sections (the orphan/widow rule, applied to
structure).

Type register (the theme's pairing, spent the document way): display carries the title
and the numbered claims, quiet and tight; body carries the measured prose at
`leading-relaxed`; the utility face carries the apparatus — masthead, folio lines,
§ and FIG numbers, table digits (`font-mono` where the theme bundles one, small caps
always tracked).

THE COLUMN (the whole layout): everything lives in ONE centered text column —
`<article className="mx-auto max-w-2xl">` inside the padded root — roughly six columns
of a twelve-column page. No side rails, no asymmetric splits, no full-bleed bands, no
inverted grounds, no alternating section backgrounds: the page is one sheet of paper.
FIGURES are the single sanctioned exception — they may widen, always symmetrically
(`@3xl:-mx-24`, up to `-mx-40` for a wide chart), so they stay centered on the column's
own axis.

PAGE BREAKS (the pacing device — spend them generously; every new idea gets a fresh
page): a numbered section opens with the SAME folio line every time, verbatim —
generous air above (`mt-24 @2xl:mt-32`), the running-header rule
(`<div className="flex items-baseline justify-between border-t border-foreground pt-2
text-[11px] uppercase tracking-widest text-muted-foreground"><span>Doc title</span>
<span>§ 02</span></div>`), then air again (`mt-14`) before the headline. The repeated
folio line is the printed page's running header; the air is the sheet edge. Break
hygiene: a figure and its caption never separate, and a heading never sits at the foot
of a page — move the break, not the caption.

FIGURES (highly encouraged — aim for one per section; this template shows rather than
decorates). Every figure is a `<figure>` with a FIG-numbered `<figcaption>`, numbered
continuously across the document. Three kinds, all first-class:
- Chart: `<Question data="$q" viz={{"kind":"vega-lite","spec":{…}}} height="380px" />`
  over a `<Query name="q">{`select … from ref_<datasetId>`}</Query>` declared in
  `<Helmet>` — Vega for every chart; quiet grays, ONE accented series, direct
  labels over legends. Evidence 380-440px; a lone number 170-220px.
- Diagram: an inline `<svg>` drawn in `currentColor` strokes at hairline weights — the
  technical-report figure. Local `url(#id)` refs only.
- Image: `<img src="ref:<imageId>" />` (publish the image artifact first; remote URLs
  are rejected).
Caption grammar: `<figcaption className="mt-3 border-t border-border pt-2 text-xs
text-muted-foreground"><span className="font-semibold text-foreground">FIG. 02</span>
— one sentence stating what the figure proves, at the data's precision.</figcaption>`

TAKEAWAYS (kept from the report tradition, but IN the column — never a side rail):
the reader must never hunt for "so what". Two placements, both minimal:
- Section close: after the last paragraph, a hairline-topped line —
  `<p className="mt-8 border-t border-border pt-3 leading-relaxed"><strong>Takeaway:</strong>
  one sentence with its number.</p>` — one per section, same register every time.
- The Summary up front is the same device aggregated: the document's 2-4 findings as
  bold-lead bullets, each with its number. State conclusions before arguing them.
No boxes, no background fills, no rail: the takeaway is a typeset line, not a widget.

Front and end matter
- Title block: a masthead rule (`flex items-baseline justify-between border-b-2
  border-foreground pb-2`, small-caps org/doc-number left, date right), then the title
  (`text-4xl @2xl:text-5xl font-semibold tracking-tight`), a muted standfirst, and a
  small-caps byline/window line. This is the cover; it earns the only motion in the
  document (`animate-fade-up`, staggered once).
- Summary: directly under the title block, hairline-bounded (`border-y border-border
  py-6`) — small-caps SUMMARY label, then 2-4 bold-lead findings, each with its number.
  A report states its conclusions before it argues them.
- Method footer: the last element — a hairline, small-caps "Method & sources", one or
  two muted lines (window, exclusions, where the data lives).

Minimal, concretely: the color budget is foreground + muted + hairlines; the accent
appears only INSIDE figures. Tables are compact hairline sets (uppercase tracked `<th>`,
`tabular-nums`, row rules). No cards, no shadows, no rounded boxes. Motion stops at the
title block — paper does not animate.

Skeleton (title block → summary → one paged section with a figure → method footer):

<div data-design="tw" className="@container bg-background px-6 py-12 text-foreground @2xl:py-16">
  <article className="mx-auto max-w-2xl">
    <header>
      <div className="flex items-baseline justify-between border-b-2 border-foreground pb-2 text-xs uppercase tracking-widest">
        <span className="font-bold">Org · Report No. 12</span>
        <span className="text-muted-foreground">13 Aug 2026</span>
      </div>
      <h1 className="animate-fade-up mt-14 text-4xl @2xl:text-5xl font-semibold tracking-tight leading-tight">The title states the finding, with its number</h1>
      <p className="animate-fade-up [animation-delay:150ms] mt-5 text-lg leading-relaxed text-muted-foreground">The standfirst says why this matters and to whom, in one sentence.</p>
      <p className="animate-fade-up [animation-delay:300ms] mt-8 text-xs uppercase tracking-widest text-muted-foreground">Author · window covered · one line</p>
    </header>
    <section className="mt-14 border-y border-border py-6">
      <p className="text-xs uppercase tracking-widest text-muted-foreground">Summary</p>
      <ul className="mt-4 space-y-2 leading-relaxed">
        <li><strong>Two-word lead:</strong> the finding in one sentence, with its number.</li>
        <li><strong>The caveat:</strong> a report that names its own risk is more credible, not less.</li>
      </ul>
    </section>
    <div className="mt-24 flex items-baseline justify-between border-t border-foreground pt-2 text-[11px] uppercase tracking-widest text-muted-foreground @2xl:mt-32">
      <span>Doc title</span><span>§ 1</span>
    </div>
    <section className="mt-14">
      <h2 className="text-2xl font-semibold tracking-tight">1. The section headline is a claim, never a topic</h2>
      <p className="mt-5 leading-relaxed">Two to four sentences set up the figure: what to look at, and what it will prove.</p>
      <figure className="my-10 @3xl:-mx-24">
        <Question data="$q" viz={{"kind":"vega-lite","spec":{}}} height="400px" />
        <figcaption className="mt-3 border-t border-border pt-2 text-xs text-muted-foreground"><span className="font-semibold text-foreground">FIG. 01</span> — what the chart proves, at the data's precision.</figcaption>
      </figure>
      <p className="leading-relaxed text-muted-foreground">The paragraph after the figure says what follows from it — never repeats it.</p>
      <p className="mt-8 border-t border-border pt-3 leading-relaxed"><strong>Takeaway:</strong> the section's one sentence to keep, with its number.</p>
    </section>
    <footer className="mt-24 border-t border-border pt-4 text-xs text-muted-foreground @2xl:mt-32">
      <p className="uppercase tracking-widest">Method & sources</p>
      <p className="mt-2 leading-relaxed">Window, exclusions, and where the data lives — one or two honest lines.</p>
    </footer>
  </article>
</div>

Do
- One centered column; the SAME folio line opening every section, verbatim; sections and
  figures numbered continuously ("§ 3", "FIG. 04").
- A figure in nearly every section — Vega chart, currentColor SVG diagram, or image —
  with a caption that states the finding.
- Generous air at every page break (`mt-24`+); keep a figure and its caption together.
Don't
- Side rails, KPI strips, boxed takeaway widgets, inverted bands, alternating
  backgrounds, cards, shadows — webpage furniture on what should be paper (the
  takeaway itself stays, as a typeset line in the column).
- Prose wider than the column; a figure widened off the column's axis; motion past the
  title block.
- Topic headlines; a section that shows nothing (no figure, no table, no number).

Components: `https://artifactbin.dev/docs/markup`; publish API: `https://artifactbin.dev/docs/llm`.
