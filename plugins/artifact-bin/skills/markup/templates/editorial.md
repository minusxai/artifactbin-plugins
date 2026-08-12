# template: editorial — Editorial

Long-read feature or board report — chaptered argument with page breakers and a takeaways rail on every section.

Confident, literary data journalism in the tradition of a NYT feature or a Stripe annual letter — or its boardroom cousin, the beautifully typeset performance report. The scroll is the pacing, headlines carry the argument, width contrast — narrow prose against wide evidence — is the design, and every section hands the reader its takeaways before letting them go.

Beats: Hero (or report masthead) → KPI strip → Chapters (3-5, one claim each, each with a takeaways rail) → Full-bleed interlude → Nuance chapter → Closing takeaway

## Editorial — the long-read feature / board report

Voice: a magazine feature or a board-grade report — never a dashboard with prose. Reading
only the headlines must deliver the whole argument; each is a CLEAN claim with a number
where the data allows ("Net revenue rose 51% and outran its budget"), set quiet
(`font-semibold tracking-tight`, sentence case) — no shouting.

PAGE BREAKERS (pick ONE style at the start; repeat it verbatim for every chapter —
consistency is what makes it feel typeset):
- Report title band: a full-bleed strip (`bg-foreground text-background -mx-6 @2xl:-mx-12
  px-6 @2xl:px-12 py-4 flex items-baseline justify-between`) — chapter title left, context
  stamp right ("May'26 vs May'25", "FY2026 · Q3"). The boardroom look.
- Newspaper rule: a double rule (`<div className="border-t-2 border-b border-foreground pt-1">`)
  under a small-caps kicker + hairline-separated chapter title. The broadsheet look.
- Ghost numeral: oversized low-contrast chapter numeral (`text-7xl font-bold
  text-muted-foreground/25`) beside the claim headline, hairline above. The magazine look.

TAKEAWAYS RAIL (every chapter, non-negotiable — the reader must never hunt for "so what"):
- Right rail (desktop): `@3xl:grid-cols-[3fr_2fr] gap-10` — evidence (chart/table) left,
  a rail right: small-caps header ("TAKEAWAYS" / "ACTION PLANS" in the report register),
  then 2-4 BOLD-LEAD bullets — `<li><strong>Concentration risk:</strong> two of three
  segments still hang on one channel (<Number …/>).</li>` — each lead a two-word label,
  each body one sentence with its live number.
- Bottom band (alternative): a full-width `bg-muted rounded-lg p-6` strip after the
  evidence with the same bold-lead bullets in 2 columns (`@2xl:columns-2`).
- Pick one placement and keep it; the rail's register (its header word, its lead style)
  is part of the design.

KPI STRIP (the report opener, right under the hero/masthead): 3-5 cells in a hairline
grid (`grid @2xl:grid-cols-5 divide-x divide-border border-y border-border`), each cell a
small-caps label, the value as a single-value embed (~170px), and the PRIOR PERIOD beneath
in muted italic ("May'25: IDR 23.2Tn" — a second tiny embed or part of the caption). The
then-vs-now subscript is what makes it read as a report, not a dashboard.

Layout grammar
- Width contrast IS the design: body copy capped at `max-w-prose`; charts break WIDER
  (`max-w-4xl`) or full-bleed (`-mx-6 @2xl:-mx-12`). Never let prose run full width.
- Alternate section backgrounds (`bg-background` / `bg-muted`); the interlude is the ONE
  inverted band (`bg-foreground text-background`) holding a single huge live number.
- Asymmetry over symmetry: `@3xl:grid-cols-[3fr_2fr]` splits, not centered stacks.
- Footnotes where claims need method: a hairline-topped `text-xs text-muted-foreground`
  line closing the chapter ("Note: balance-sheet figures as of May-26, single data point.").
- Motion, rationed to the report register: `animate-fade-up` on the hero (staggered
  standfirst), `reveal-up` on each chapter's evidence block. No marquee, no ambient
  loops — the boardroom does not blink.

Skeleton (hero → KPI strip → one chapter with breaker + takeaways rail → interlude → close):

<div data-design="tw" className="@container px-6 @2xl:px-12">
  <header className="py-16 @2xl:py-24 max-w-4xl">
    <p className="animate-fade-in text-xs uppercase tracking-widest text-muted-foreground">Eyebrow · names the beat, ~4 words</p>
    <h1 className="animate-fade-up mt-4 text-5xl @2xl:text-7xl font-bold tracking-tight leading-[1.05]">The headline states the finding, with its number</h1>
    <p className="animate-fade-up [animation-delay:200ms] mt-6 text-lg text-muted-foreground max-w-prose">The standfirst earns the scroll in one sentence: why this matters and to whom.</p>
  </header>
  <section className="grid @2xl:grid-cols-4 divide-x divide-border border-y border-border">
    4 cells: small-caps label → single-value embed (~170px) → muted italic prior period beneath
  </section>
  <section className="py-16">
    <div className="-mx-6 @2xl:-mx-12 px-6 @2xl:px-12 py-4 bg-foreground text-background flex items-baseline justify-between">
      <h2 className="text-2xl font-semibold tracking-tight">01 · Chapter title: a claim, never a topic</h2>
      <span className="text-xs uppercase tracking-widest opacity-70">FY26 vs FY25</span>
    </div>
    <div className="mt-10 grid @3xl:grid-cols-[3fr_2fr] gap-10 items-start">
      <div>
        <p className="max-w-prose text-muted-foreground">Two to four sentences set up the chart — what to look at and why it matters.</p>
        <div className="reveal-up mt-6"><Question id={N} height="430px" /></div>
        <p className="mt-3 text-sm text-muted-foreground">The caption states what the chart proves, at the data's precision.</p>
      </div>
      <aside className="reveal-up [transition-delay:150ms] border-t-2 border-foreground pt-4">
        <p className="text-xs uppercase tracking-widest text-muted-foreground">Takeaways</p>
        <ul className="mt-4 space-y-3 text-sm">
          <li><strong>Two-word lead:</strong> one sentence, one live number.</li>
          <li><strong>The risk:</strong> named inside the win, with its figure.</li>
        </ul>
      </aside>
    </div>
    <p className="mt-10 border-t border-border pt-3 text-xs text-muted-foreground">Note: method or window caveat, one line.</p>
  </section>
  <aside className="-mx-6 @2xl:-mx-12 my-16 bg-foreground text-background py-20 px-6 text-center">
    <p className="text-sm uppercase tracking-widest opacity-70">The interlude: one number, no chart, full width</p>
    <div className="reveal-scale mt-6 mx-auto max-w-md"><Question id={N} height="200px" /></div>
  </aside>
  <section className="py-16 border-t border-border">
    <h2 className="text-3xl font-semibold max-w-2xl">The takeaway restates the lead with its earned weight</h2>
    <ul className="mt-6 space-y-3 max-w-prose">2-3 quantified bold-lead takeaways answering "so what should the reader do?"</ul>
  </section>
</div>

Do
- One idea per chapter; the SAME breaker and the SAME rail placement every chapter.
- Give the nuance its own chapter ("dependable, and exposed") — one flagged caveat is more persuasive than none.
- Vary chart heights with the beat (evidence 400-460px, single-values 170-260px).
Don't
- Every section a Card; centered-everything; all charts one height; topic headlines.
- A chapter that ends without takeaways; mixed breaker styles; a second interlude.

Components: `https://artifactbin.dev/docs/markup`; publish API: `https://artifactbin.dev/docs/llm`.
