---
name: "design"
description: "Design fundamentals for artifact-bin documents (hierarchy, spacing, color, typography). Read before authoring an artifact-bin markup artifact when no more specific design skill applies."
---

# artifact-bin design guide

Read this before authoring anything a human judges with their eyes. Approach
every artifact as the design lead at a small studio known for versatility:
deliberate choices about palette, type, and layout, pitched at the treatment
the task actually calls for — never templated. The kit gives you components;
this page is about using FEWER of them, better.

## Calibrate the treatment

- A doc deserves the same craft as a landing page — what changes is the
  treatment. A plan, memo, or working report wants a UTILITARIAN treatment:
  real hierarchy, considered spacing, a proper palette, no giant hero,
  flourishes tasteful and few.
- Something the user will keep, present, or share — a launch page, a deck for
  an audience, a flagship report — wants an EDITORIAL treatment: opinionated
  calls, one real aesthetic risk where it serves the work.
- When unsure: a well-composed page is never the wrong answer; an
  over-designed one sometimes is.

## Ground it in the subject

- Pin one concrete subject, its audience, and the page's single job before
  authoring. Distinctive choices come from the subject's own world — its
  materials, instruments, vernacular — not from a house style.
- Build with real content throughout, never lorem placeholder.

## Plan before you build

Sketch a compact plan first: which theme and WHY (https://artifactbin.dev/docs/themes), the
layout concept in a sentence, where the one bold moment goes, and what the
subject's MOTIF is (below). Templates (https://artifactbin.dev/docs/templates) are genre
references, not contracts — pick one when the content is genuinely that
genre, deviate where the subject knows better, and omit the template
entirely when the subject suggests its own structure. If any part of the
plan reads like the generic default you would produce for any similar page,
revise that part. Then build to the plan.

## The subject motif

The strongest pages carry ONE device drawn from the subject's own world and
commit every label to it: a video explainer whose chapters are frame ranges
under a drawn frame-ruler; an incident report typeset as a flight recorder
log; shipping data wearing a harbor-master's console. This is the scrolly
template's "conceit" — but it works at ANY register, deadpan as well as
theatrical, and it does not need the scrolly costume: a sober technical
explainer with mono chapter stamps and an inline-SVG motif reads as designed
without a single joke. Invent the motif from the subject, express it in the
chapter labels, the figure numbering, and one small drawn SVG element — then
stop; one motif, total commitment, no second conceit.

## Hierarchy

- One idea per viewport. A headline states the FINDING ("Churn halved after
  onboarding"), never the topic ("Churn analysis").
- Contrast in size is the design: small uppercase tracked eyebrows against
  large tight headlines beats five mid-sized headings.
- Body copy caps at `max-w-prose` (~65ch); evidence (charts, tables) breaks
  wider. Width contrast IS the layout.

## Typography

Typography carries the page even when the page isn't about typography.

- The PAIRING comes with the theme: a display face on headings, a body face on
  everything else, and (in most themes) a mono utility face — chosen as a set.
  Pick the theme whose pairing matches the document's voice (`/docs/themes`)
  and never fight it with a second family; the one sanctioned accent is a
  color, not a font.
- Spend the pairing in three ROLES. DISPLAY carries the argument: few, large,
  `tracking-tight`, `text-balance`, sentence case. BODY carries reading:
  `leading-relaxed`, capped at `max-w-prose`. UTILITY (`font-mono` where the
  theme bundles one) carries the apparatus — eyebrows, folios, figure numbers,
  axis and table digits, captions.
- Set a SCALE and stay on it: 3-4 sizes per document with a real jump between
  roles (`text-xs` apparatus · `text-base`/`text-lg` body · `text-4xl`+
  display). Two adjacent sizes doing different jobs reads as a mistake.
- Uppercase belongs to the apparatus only, always tracked (`tracking-widest`);
  never letterspace lowercase body copy. Aligned digits get `tabular-nums`.
- Weight is hierarchy's cheapest lever — `font-semibold` headings over bold
  paragraphs; if everything is bold, nothing is.
- No webfont URLs: authored `<style>` blocks strip every external `url()` /
  `@import` at save (exfiltration + self-contained-capture guards), so a
  Google Fonts link is silently inert. A `data:` URI `@font-face` passes,
  but the platform way is the theme's bundled pairing.

Every template names how these roles are spent (its "Type register") — the
genre changes the register, never the roles.

## Space

- Whitespace is structure, not waste. Pad sections generously (`py-16`) and
  let the gap between sections exceed the gap within one.
- Space sibling groups with flex/grid `gap-*`, not per-element margins that
  collapse or double.
- Align to the grid; when in doubt, flush left. Centered body copy is almost
  always wrong.
- Wide content (tables, code) gets its own `overflow-x-auto` container — the
  page never scrolls sideways.

## Color

- In `markup`, theme tokens are the default (`text-muted-foreground`,
  `bg-muted`, `border-border`, `--chart-1..5`) — the theme owns hue, and a
  page built on hex fights it. ONE bespoke accent (`text-[#e2483d]`) is the
  sanctioned exception when the subject demands a hue no theme carries.
- ONE accent, meaning one thing (the subject, the exception). Context sits in
  grays. If everything is highlighted, nothing is.
- Semantic state color (good / warning / critical) is separate from the
  accent and doesn't count as your accent.

## Motion

- Leverage motion deliberately. Think about where and if animation can
  serve the subject: a page-load sequence, a scroll-triggered reveal,
  hover micro-interactions, ambient atmosphere. An orchestrated moment
  usually lands harder than scattered effects; choose what the direction
  calls for. Sometimes less is more — extra animation contributes to the
  feeling that a design is AI-generated.
- You have real CSS *and* real JavaScript: custom `@keyframes` in the
  Helmet's `<style>` block attached to classes, a `<Helmet><script>` that
  runs in the served document (attach handlers with `addEventListener`), the kit's one-class shortcuts, and the scroll-reveal
  observer contract (`data-reveal` + `data-mx-seen`) for
  animate-when-visible without JS — see https://artifactbin.dev/docs/markup "Motion".
  Utilities compile `!important` and always beat authored CSS: design
  the two layers, don't fight them.
- Everything fails open where it must: captures, exports, edit mode, and
  reduced-motion viewers see the finished page.

## Charts

- The title states the takeaway; axes and units do the explaining.
- Direct labels beat legends; gridlines stay barely-there; no gradients,
  shadows, or 3D — data-ink only.
- Every number a viewer might question binds to a real `ref:` dataset.

## Copy is design material

- Write from the reader's side of the screen: name things by what people
  recognize, not how the system is built.
- Active voice; a control says exactly what happens. Errors say what went
  wrong and how to fix it — no apologies, no vagueness.
- Specific beats clever.

## Structure is information

- Eyebrows, numbering, dividers, and labels must encode something TRUE about
  the content, not decorate it.
- Numbered markers (01 / 02 / 03) only when the content really is a sequence
  whose order the reader needs.

## When it is a UI, not a document

- A dashboard is scanned and operated, not read: summary before detail;
  what needs attention reads at a glance.
- Encode state in form as well as number — a badge, a severity stripe — and
  make what's interactive look interactive.

## Avoid the default AI look

Unprompted AI design clusters around a few looks: cream + serif + terracotta;
near-black with one acid accent; a purple-blue gradient hero; emoji as
section markers; everything centered; rounded cards with accent rails. If
your user asks for one of these, follow their words exactly; otherwise don't
spend your freedom there — pick the theme and structure from the SUBJECT, not
from habit.

## Restraint

- Prefer plain typography over one more Card. Decoration that carries no
  information comes out.
- Spend your boldness in ONE place; keep everything around it quiet.
- Pick the theme for the mood and let it work — do not re-style what it
  already styles.

## What the theme does not do for you

The above is mostly free in `markup` (the theme carries fonts, palette, and
color modes). What your own Helmet CSS adds, you carry yourself:

- The CSP blocks font CDNs — a linked webfont silently falls back. Inline an
  `@font-face` data URI or design on a system stack.
- Handle both color schemes via `prefers-color-scheme`, or commit to one and
  paint the background and every color explicitly — a transparent body
  borrows whatever ground the viewer's browser paints.
- Choose your neutrals: a grey with a slight hue bias toward the accent reads
  as chosen; a pure mid-grey reads as unconsidered.
- Watch selector specificity (don't let two classes fight over section
  spacing), close every element, give keyboard focus a visible state, respect
  `prefers-reduced-motion`.

Then author with the vocabulary: https://artifactbin.dev/docs/markup (components),
https://artifactbin.dev/docs/themes (design systems), https://artifactbin.dev/docs/templates (genres).
