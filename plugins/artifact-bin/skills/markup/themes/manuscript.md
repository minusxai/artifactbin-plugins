# theme: manuscript — Manuscript

Serif editorial — cream paper, sepia ink, oxblood accent; warm ink with ochre in dark. Cormorant Garamond display over Noto Serif.
Fonts: display Cormorant Garamond, body Noto Serif.
Default mode: light (set `colorMode` to pin the other; readers can flip at view time).

Manuscript — the serif slot: bookish print with a newsroom's discipline. Serif air, generous
leading, hairline rules; the oxblood accent is an illuminator's ink — initials, rules, the
focal series — never large fills. Drop caps and small-caps kickers are welcome; figures may
sit in the margin like plates in an atlas; tables are first-class graphics with strong
header rules. In dark the page is warm brown-black under a reading lamp — cream text, the
accent turning ochre — never a cold screen inversion.
Charts: muted earth tones with the accent focal series; serif axis labels feel right here.
Structural: `<hr>` is a hairline; `<blockquote>` sets italic with a fine left rule; table
`<th>` sets in tracked small caps — native tables already look typeset.
Do: wide margins, `max-w-prose` columns, numbered figures with captions ("Fig. 2 — …"),
multi-column long prose (`@3xl:columns-2`), datelines.
Don't: neon or saturated hues, heavy shadows, condensed grotesque headlines, large radii,
dense borderless tables.

Author with token classes (`text-muted-foreground`, `bg-muted`, …) and the
theme does the rest — hardcoded palettes fight it. Components:
`https://artifactbin.dev/docs/markup`.
