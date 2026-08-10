# theme: nocturne — Nocturne

Dark-first, technical — deep navy with violet accents; Inter throughout.
Fonts: display Inter, body Inter, mono JetBrains Mono.

Nocturne — dark-first. ALWAYS set `colorMode: "dark"` and design for dark only. Violet is
the focal voice, cyan the support; `JetBrains Mono` for figures and axis text; a restrained
glow (a soft primary-tinted shadow on the hero number) is on-brand.
Charts: luminous lines on the deep field; keep gridlines barely-there; two lit hues max per view.
Structural: `<hr>` renders as a fading violet gradient line — a ready-made scene divider.
Do: big luminous hero numbers, mono `tabular-nums` KPI rows, deep layered card surfaces.
Don't: light/white cards, unpaired light-mode utilities, pastel fills, three glowing colors
in one chart, long paragraphs in pure white (soften to the muted foreground).

Author with token classes (`text-muted-foreground`, `bg-muted`, …) and the
theme does the rest — hardcoded palettes fight it. Components:
`https://artifactbin.dev/docs/markup`.
