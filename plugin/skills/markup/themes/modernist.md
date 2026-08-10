# theme: modernist — Modernist

Stark Swiss editorial — white, near-black, one red accent; Archivo display over Inter, zero radius.
Fonts: display Inter, body Inter, mono JetBrains Mono.

Modernist — Swiss editorial discipline. The red `--primary` is the ONLY hue on the page and
it always means the subject; everything else is ink on paper. Keep everything flush left —
headlines, copy, even wide CTA-like elements; centered text is off-brand. Dividers are
strong (`border-t-2 border-foreground`), never decorative hairlines everywhere.
Charts: all series in grays (`--muted-foreground`), the focal series red; direct labels over
legends; no gradient fills.
Structural: `<hr>` renders as a strong 2px ink rule and `<blockquote>` carries a bold top
rule — use them as section dividers instead of hand-styled borders.
Do: visible grid, generous white, uppercase tracked eyebrows, one red element per viewport.
Don't: round a corner (radius is 0 on purpose), gradients, shadows, a second accent hue,
tinted or decorative imagery.

Author with token classes (`text-muted-foreground`, `bg-muted`, …) and the
theme does the rest — hardcoded palettes fight it. Components:
`https://artifactbin.dev/docs/markup`.
