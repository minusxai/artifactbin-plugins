# template: deck — Deck

Presentation deck — full-viewport slides in acts; quiet paper slides between full-bleed accent dividers.

A boardroom keynote rendered as a scroll: one idea per full-viewport slide, headlines you could speak aloud to the room, generous emptiness as the luxury. The deck moves in ACTS — quiet paper slides punctuated by full-bleed solid-accent dividers — and its structure is announced (contents, numbered acts), never discovered.

Beats: Cover → Contents (mirrors the act titles) → Act divider (solid accent, per act) → Content slides (statement / columns / chart / table / big number) → Quote or image slide (a breather per act) → Timeline / roadmap → Close

## Deck — a presentation that scrolls

Voice: a keynote. Every slide headline is a spoken sentence ("Retention pays for the price
increase"), never a label ("Retention"). One idea per slide. A good slide is ~40% empty —
put the air BELOW the content, not around it.

THE SLIDE (every slide, no exceptions; fixed/sticky are banned — a slide is a tall section):
- The deck is `<SlideDeck>` wrapping one `<Slide>` per slide. `<Slide>` already fills the
  reader's real viewport (`--mx-vh`, platform-provided, with a headless fallback) as a flex
  column — add the dressing per slide: `<Slide title="…" className="border-b border-border py-14">`.
  Never vh units (broken in this surface); never a raw `<section>` for a slide — Slide is what
  powers the reader's slide overview and present-mode paging.
- Give EVERY slide a short spoken `title` (it names the slide in the overview rail and the
  present controls; untitled slides fall back to their first heading).
- HEADER BAND on every content slide: kicker `<p className="text-xs uppercase tracking-widest font-semibold text-primary">01 · ACT NAME</p>`
  + hairline `<hr className="mt-3 mb-10">`; then the slide h2. Same gutters, same band, every slide — the repetition IS the design.
- FOOTER META closes every content slide: `<div className="mt-auto pt-10 flex justify-between text-xs text-muted-foreground"><span>Deck title — date</span><span className="font-semibold">07</span></div>`.

ACTS AND COLOR RHYTHM (the interleaving that makes it feel designed):
- Group slides into 2-4 numbered acts. Each act OPENS with a full-bleed solid-accent divider:
  `<section className="min-h-[var(--mx-vh,760px)] flex flex-col justify-center bg-primary text-primary-foreground -mx-6 @2xl:-mx-12 px-6 @2xl:px-12">`
  carrying a giant TONE-ON-TONE act numeral (same hue, darker: `<span className="text-9xl font-bold text-primary-foreground/25">02</span>`
  — or a darker literal of the accent) and the act title below it. Nothing else on the slide.
- Dividers are the ONLY saturated slides; everything between is quiet paper. That alternation
  (paper… paper… ACCENT… paper) is the pulse of the deck — never color a content slide's ground.
- The contents slide lists the acts as numbered rows (accent numerals, hairline row rules,
  `tabular-nums`) whose titles mirror the dividers EXACTLY.

SLIDE TYPES (pick per beat; each stays one idea):
- Statement: at most 3 one-line bullets with square accent markers
  (`<span className="inline-block size-2 bg-primary mr-3">`), then air. No paragraphs.
- Columns (2 or 3): equal cells on one gutter, and EACH COLUMN OPENS UNDER A RULE
  (`<div className="border-t-2 border-foreground pt-3">` heading + short copy); columns top-align
  and share the line rhythm. Collapse to one column on phones (`@2xl:grid-cols-3`).
- Quadrants: 2x2 grid with hairline cross rules (`divide-x divide-y divide-border`) and tiny
  muted axis labels at the edges ("← less … more →").
- Chart slide: claim headline + ONE `<Question>` (430-460px, ≥ 60% width) + one takeaway line
  under it. Restyle via `viz` override: all series in grays, ONE accent element (the bar/line
  being discussed), direct labels, no legend where possible. Never two charts on a slide.
- Big-number slide: one enormous accent figure — a styled single-value embed
  (`valueColor` = the accent, huge `valueFontSize`) — plus a two-line muted caption. NOTHING else.
  Use once or twice as an act's exclamation point.
- Table slide: a compact table; uppercase tracked `<th>`, hairline row rules, `tabular-nums`.
- Quote slide: display-size quote in `text-muted-foreground` (softer than headlines, not black),
  hanging quote mark, `— attribution` line. A breather between dense slides.
- Timeline: a horizontal rail — uppercase tracked date labels over square nodes over bold
  labels + muted notes, one left edge per milestone; the CURRENT step is the one accent square.
- Cover and Close share one grammar: kicker + hairline, huge flush-left title
  (`text-6xl @2xl:text-8xl tracking-tight`), one-line muted standfirst, byline/date at the foot.
  The close restates the lead number and the next step, then contact.

Skeleton (cover → divider → chart slide; extend the pattern per act):

<div data-design="tw" className="@container px-6 @2xl:px-12">
  <SlideDeck>
    <Slide title="Cover" className="border-b border-border py-14">
      <p className="text-xs uppercase tracking-widest font-semibold text-primary">Company · Quarter</p>
      <hr className="mt-3" />
      <h1 className="mt-auto text-6xl @2xl:text-8xl font-bold tracking-tight max-w-4xl">The title states the one finding</h1>
      <p className="mt-6 text-lg text-muted-foreground max-w-xl">A standfirst no longer than the breath it takes to say it.</p>
      <div className="mt-auto pt-10 flex justify-between text-xs text-muted-foreground"><span>Author</span><span>Date</span></div>
    </Slide>
    <Slide title="Act one" className="justify-center bg-primary text-primary-foreground -mx-6 @2xl:-mx-12 px-6 @2xl:px-12">
      <span className="text-9xl font-bold text-primary-foreground/25">01</span>
      <h2 className="mt-2 text-4xl @2xl:text-5xl font-semibold">Act title, mirrored in the contents</h2>
    </Slide>
    <Slide title="The takeaway, spoken" className="border-b border-border py-14">
      <p className="text-xs uppercase tracking-widest font-semibold text-primary">01 · ACT NAME</p>
      <hr className="mt-3 mb-10" />
      <h2 className="text-3xl @2xl:text-5xl font-semibold max-w-3xl">The headline is the takeaway, spoken aloud</h2>
      <div className="mt-10 max-w-5xl"><Question id={N} height="440px" /></div>
      <p className="mt-4 text-sm text-muted-foreground max-w-prose">One line under the chart: what to remember when the slide is gone.</p>
      <div className="mt-auto pt-10 flex justify-between text-xs text-muted-foreground"><span>Deck title — date</span><span className="font-semibold">07</span></div>
    </Slide>
  </SlideDeck>
</div>

Do
- Speakable headlines; huge type, few words; the air below the content.
- One accent element per figure; contents ↔ dividers mirrored to the word.
Don't
- Color a content slide's ground; two charts on a slide; paragraphs (three lines max).
- Scroll-snap/parallax tricks; cramming a slide to avoid adding one — add the slide.

Components: `https://artifactbin.dev/docs/markup`; publish API: `https://artifactbin.dev/docs/llm`.
