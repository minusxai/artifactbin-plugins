# theme: broadsheet — Broadsheet

Newspaper/report — paper white, ink, steel blue; Source Serif 4.
Fonts: display Noto Serif, body Noto Serif.

Broadsheet — newsprint. Ink on paper with steel-blue as the highlighter; column rules
(`divide-x` hairlines), small-caps kickers, serif headlines, and tables treated as
first-class graphics with strong header rules.
Charts: ink-gray series with the steel-blue focal; direct labels over legends; no fills
brighter than the accent.
Structural: `<hr>` renders as the classic double newspaper rule and table `<th>` sets in
tracked small caps — native tables already look typeset.
Do: multi-column layouts (`@3xl:columns-2` for long prose), datelines, ruled section
headers, dense-but-ordered stat tables.
Don't: playful color, large radii, gradients, oversized decoration, centered body copy.

Author with token classes (`text-muted-foreground`, `bg-muted`, …) and the
theme does the rest — hardcoded palettes fight it. Components:
`https://artifactbin.dev/docs/markup`.
