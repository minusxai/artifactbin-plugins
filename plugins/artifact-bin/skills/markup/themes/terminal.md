# theme: terminal — Terminal

Terminal — mono type throughout, near-black with neon green by default; a paper terminal in light.
Fonts: display JetBrains Mono, body JetBrains Mono, mono JetBrains Mono.
Default mode: dark (set `colorMode` to pin the other; readers can flip at view time).

Terminal — green phosphor on glass. Mono type for EVERYTHING (headings included — that is the voice), and
it opens dark by default: near-black ground, neon green primary, cyan and magenta as chart
support. A restrained glow (a soft primary-tinted shadow on the hero figure) is on-brand;
prompt markers (`$`, `>`), tracked uppercase kickers and `tabular-nums` KPI rows carry the
register. The light mode is a paper terminal — warm off-white with the green darkened to
ink weight — never a generic white page.
Charts: luminous lines on the dark field, gridlines barely-there, two lit hues max per view;
direct labels in mono.
Structural: `<hr>` is a thin primary rule (a scanline) and `<blockquote>` carries a green
left bar — ready-made section dividers.
Do: big mono hero numbers, ASCII-flavored ornaments used sparingly, status chips
(`● live`), dense ordered tables.
Don't: serifs anywhere, rounded-2xl softness, pastel fills, three glowing colors in one
chart, long paragraphs in pure neon (body copy stays in the muted foreground).

Author with token classes (`text-muted-foreground`, `bg-muted`, …) and the
theme does the rest — hardcoded palettes fight it. Components:
`https://artifactbin.dev/docs/markup`.
