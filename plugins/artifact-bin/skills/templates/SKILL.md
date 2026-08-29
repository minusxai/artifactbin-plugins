---
name: templates
description: "How to choose a genre when the ask does not name one. The brief already carries a usable grammar for all four — read this only to compare genres; then only the chosen genre's file, if at all."
---
## Read first

Pick ONE by the content's shape, then read its file for the beats and layout
grammar — details about a genre you didn't pick are noise:

- `editorial` — Typeset document/report — one centered text column paced in pages by a repeated folio rule, with numbered figures as the only wide elements. → [`editorial.md`](editorial.md)
- `deck` — Presentation deck — full-viewport slides in acts; quiet paper slides between full-bleed accent dividers. → [`deck.md`](deck.md)
- `scrolly` — Playful scrollytelling — a pudding.cool-style data story with a conceit, ticker bands, and chapter breaks. → [`scrolly.md`](scrolly.md)
- `dashboard` — Operating view — a full-screen grid of live tiles obeying a shared control row; almost no prose, nothing bold. → [`dashboard.md`](dashboard.md)

Choosing: when the ask clearly names a genre (slides → `deck`, operating
view → `dashboard`, board report / long-read → `editorial`), pick it. When
it is NOT obvious from the user's instructions, **default to `scrolly`** —
its conceit-led, designed treatment (at whatever register the subject can
carry, deadpan included) is the strongest default for an unspecified ask.
If you are genuinely torn between readings, clarify with the user and offer
the candidate genres as options rather than guessing.

## What a template is

A `template` is the document's structural GENRE — its beat structure and
layout grammar — orthogonal to the design `theme`, which is purely a token
set ([../themes/SKILL.md](../themes/SKILL.md)). Set it as the top-level
`template` field of the publish call. It is a REFERENCE, not a contract: each
file documents a genre's beats as a proven starting point, and a structure
derived from the subject itself beats any of them. Deviate deliberately, or
omit the `template` field and go bespoke — that is a first-class choice, not
a fallback. The component vocabulary every genre is built from:
[../markup/SKILL.md](../markup/SKILL.md).
