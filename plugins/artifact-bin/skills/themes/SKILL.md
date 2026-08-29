---
name: themes
description: "What a theme is and how to override tokens. The brief already lists the six and the token classes — read this only for :root overrides."
---
## Read first

Pick ONE by the subject's mood, then read its file for the full authoring
guidance — details about a theme you didn't pick are noise:

- `modernist` — Stark Swiss editorial — white and near-black with one red accent, inverted to ink-black in dark. Zero radius. → [`modernist.md`](modernist.md)
- `organic` — Warm, soft, playful — sage green, terracotta, leafy chart tones; deep moss in dark. Extra-round corners. → [`organic.md`](organic.md)
- `industry` — Professional, square — slate and industrial blue, safety-orange for the one exception; dark slate in dark. → [`industry.md`](industry.md)
- `terminal` — Terminal — mono type throughout, near-black with neon green by default; a paper terminal in light. → [`terminal.md`](terminal.md)
- `manuscript` — Serif editorial — cream paper, sepia ink, oxblood accent; warm ink with ochre in dark. Cormorant Garamond display over Noto Serif. → [`manuscript.md`](manuscript.md)
- `pop` — Playful and loud — candy magenta, cyan and amber over near-white, chunky radii, heavy Bricolage Grotesque headings; deep plum in dark. → [`pop.md`](pop.md)

Author with token classes (`text-muted-foreground`, `bg-muted`, `bg-primary`,
`border-border`, …) and the theme does the rest — hardcoded palettes fight it.
In every theme "accent" means the `primary` token (`text-primary`,
`bg-primary`); the alert/exception colour is `destructive`; the CSS token
`--accent` is a quiet neutral surface tint, never the accent.

## What a theme is

A `theme` is a complete design system — fonts plus the full token palette
(`--background`/`--foreground`/`--primary`/`--chart-1..5`/…) every kit
component and Tailwind token class follows. Set it as the top-level `theme`
field on a `markup` artifact; humans can switch it later in the editor
without recompiling. Every theme carries BOTH a light and a dark palette:
`colorMode` sets the author's default, readers can flip the rendered mode at
view time, and theme token classes stay legible in both.

Overriding a theme's tokens with ordinary `:root` CSS in the Helmet style
block — and the full list of palette and type keys — is documented once, in
the `<Helmet>` section of [../markup/SKILL.md](../markup/SKILL.md).
