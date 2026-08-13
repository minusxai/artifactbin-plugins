# template: scrolly — Scrolly

Playful scrollytelling — a pudding.cool-style data story with a conceit, ticker bands, and chapter breaks.

Curious, witty, second-person — and DRESSED UP: the story wears a costume (a mission console, a field report, a race broadcast) and every element plays along. The scroll IS the story, suspense lives between chapters, and fun, visual stunningness is the point — serious about the data, theatrical about everything else. The costume itself can run deadpan (a technical console, straight-faced) or full broadcast — pick the register the subject can carry.

Beats: Console hero / cold open (the conceit announces itself) → Ticker band → Chapters (3-5), each opened by a page-break band → Twist → Payoff → Methodology footer

## Scrolly — the playful data story

Voice: curious, witty, second person ("you'd expect X — that's not what happened").
Honesty rules hold: the humor is in the telling, never at the data's expense.

Type register (the theme's pairing, spent the console way): the utility face IS the
conceit's voice — mono uppercase tracked for every label, ticker, stamp, and spec
strip; display shouts the two-tone claims, condensed and huge; body stays in short
two-column bursts between them.

THE CONCEIT (the single biggest lever): invent a fiction the data lives inside — a mission
console, a case file, a broadcast — and commit EVERY element to it: name the fake console
("STRAIT WATCH · TRAFFIC MONITOR"), chapter labels in its voice ("SITREP 02 · THE
CHOKEPOINT"), figure numbers ("FIG.03"), status chips ("● STATUS: CLOSED"), timestamps
("AS OF 17 JUN · 00:00Z"). Mono uppercase letter-spaced type (`font-mono text-xs uppercase
tracking-widest`) is the conceit's voice for ALL labels. One conceit, total commitment.

THE FUN KIT (compose 4-6 per story; pace them, never all on one screen):
- Console topbar: a slim full-bleed bar opening the story — conceit name left, status chip
  and timestamp right, strong rule below. It scrolls away (fixed/sticky stay banned).
- Two-tone headline: huge condensed uppercase claim with THE one word in the accent —
  `<h1 className="animate-fade-up text-6xl @2xl:text-8xl font-bold uppercase tracking-tight">The strait went <span className="text-primary">dark.</span></h1>`
  (the hero one enters with `animate-fade-up`; chapter ones ride `reveal-up` instead).
- Ticker band: a full-bleed accent strip of repeating key stats
  ("··· CARGO -97% ··· DAILY 95→5 ··· WAR 28 FEB ···") that actually SCROLLS: an
  `overflow-hidden` band around `<div className="flex w-max animate-marquee">` whose
  content appears twice (two identical spans) so the -50% loop repeats seamlessly;
  `[animation-duration:20s]` tunes the speed. Use it as a section seam once or twice.
- Scroll reveals: each chapter's evidence enters as the reader reaches it — `reveal-up`
  on the chart slab, staggered stat cards (`[transition-delay:120ms]`, `240ms`). One
  reveal beat per chapter; captures and reduced-motion viewers always see the page
  finished.
- Drawn motif: one small inline `<svg>` in the conceit's voice — the console's frame
  ruler, the case file's route map — `currentColor` strokes so it follows the theme,
  local `url(#id)` refs only.
- Custom choreography: when the kit's motion isn't enough, write your own `@keyframes`
  in the document's `<style>{`...`}</style>` block (marching-ants borders, a sweeping
  radar line, a typed-on heading) — class-scoped, reduced-motion-guarded, and never
  fighting a utility (utilities are !important).
- Page-break band: chapters open with a full-bleed inverted strip (`bg-foreground
  text-background -mx-6 @2xl:-mx-12 py-10`) carrying a small centered mono label ("↓
  SITREP 04 · THE STANDOFF NOW") and a GIANT ghost chapter numeral bleeding off one edge —
  a huge 15%-opacity numeral in the accent. Large type IS the decoration.
- Stamped section label: the chapter kicker in a bordered, slightly rotated box
  (`reveal-left inline-block border-2 border-foreground px-3 py-1 -rotate-2 font-mono
  text-xs uppercase tracking-widest`) sliding in from the left, with an italic muted
  aside sitting beside it — ONE flex row (`flex flex-wrap items-baseline gap-x-4`)
  sitting full-width ABOVE the chapter headline. Never park the label in its own grid
  column: two small lines beside a tall chapter is a mostly-empty rail at desktop widths.
- Hard-shadow chart slab: charts sit in framed cards with an OFFSET SOLID shadow — a solid
  color block behind the graph, no blur, printed-poster feel: `reveal-up border-2
  border-foreground shadow-[8px_8px_0_0_var(--primary)]` (or the foreground token;
  alternate per chapter) — each slab reveals as its chapter is reached.
- Figure spec strip: under each chart, a dashed-top footer row — `FIG.02 · WHAT IT SHOWS`
  left, an accent annotation right ("MAR ONWARD: THE GAP").
- Dashed stat cards: `border-2 border-dashed border-foreground/60` cards — mono kicker, a
  giant accent single-value embed, one-line caption. Arrow notation for change ("3,253 → 129"
  — as a suffix in the embed's columnFormats, still live). Stagger a row of them:
  `reveal-up`, then `reveal-up [transition-delay:120ms]`, `[transition-delay:240ms]`.
- Chip row: small bordered mono tags of the key facts under the hero ("TANKERS 43%",
  "DAILY 95→5", "● CLOSED").
- Texture: a faint blueprint grid on quiet sections via Tailwind arbitrary background
  utilities (a `linear-gradient` plus `bg-[size:48px_48px]`), barely-there.
- Drop cap: each chapter's first paragraph opens with a huge accent initial (floated
  `<span>`); pure ornament — every data number is still a live embed.

STRUCTURE: console hero (conceit topbar → two-tone claim → one-sentence setup → the ONE
stat enormous with its spec caption → chip row) → ticker seam → chapters, each: page-break
band → stamped label + italic aside → two-tone headline → short 2-column prose
(`@3xl:grid-cols-2`, three sentences per column max) → slab-framed chart with spec strip,
dashed stat cards beside it (`@3xl:grid-cols-[3fr_2fr]`) → the TWIST chapter breaks the
pattern (inverted ground, or the evolving chart: re-embed the SAME question across steps
with progressive `viz` overrides — gray, then one series accented, then the domain zoomed;
the platform's scrolly scene, no sticky needed) → PAYOFF: the full-width chart and the
moral in one huge two-tone sentence → small honest methodology footer in the conceit's
voice ("END OF TRANSMISSION · sources, window, exclusions").

Only the BANDS run full-bleed (ticker, page-break, inverted twist); each chapter's
content lives in a centered width-capped shell (`mx-auto max-w-6xl`) so the story
alternates bleed and column instead of stretching edge to edge. Every chapter opens
the same way — band, label row, headline — repeated verbatim; the twist is the one
sanctioned break.

BALANCE the grid's weight: a split (`@3xl:grid-cols-[3fr_2fr]`) is earned only when
both columns carry comparable height — prose one side, evidence the other. If one
cell would hold just a label, a stamp, or a lone stat, stack it above instead; a
short column beside a tall one reads as a hole in the page, not as asymmetry.

Do
- Commit to the conceit in every label; alternate dark/light grounds between chapters.
- One reveal per chapter; direct-label the thing being followed; captions do jokes AND work.
Don't
- Fake sticky scenes; walls of prose; two conceits; a payoff without its number; the whole
  fun kit on one screen.
- A chapter label in its own side column; chapter prose running full width uncapped.

Components: `https://artifactbin.dev/docs/markup`; publish API: `https://artifactbin.dev/docs/llm`.
