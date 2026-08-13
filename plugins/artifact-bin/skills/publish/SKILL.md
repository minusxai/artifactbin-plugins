---
name: "publish"
description: "Publish self-contained HTML artifacts (reports, dashboards, decks, datasets, charts, images) to artifact-bin and share the public link. Use when the user wants to publish, share, or host a page, report, dashboard, deck, dataset, chart, or image, or mentions artifact-bin."
---

With this plugin installed, prefer the artifact-bin MCP tools
(`create_artifact`, `update_artifact`, `edit_artifact`, `get_artifact`,
`list_artifacts`, `list_versions`, `get_version`, `revert_artifact`,
`delete_artifact`) — they speak the exact protocol below over the same
tokens, so the HTTP reference doubles as the tools' semantics (content
tiers, edit protocol, error meanings). Read the `markup` skill in this
plugin before authoring markup, and the `design` skill for design
fundamentals.

# artifact-bin

Upload a self-contained HTML artifact, get back a **public link** to share with your user.

## Auth — get a token (in order of preference)

Every `/api/artifacts` call needs a bearer token:

```
Authorization: Bearer mx_...
```

1. **Saved config** — check `~/.config/artifact-bin/config.json` for
   `{ "url", "token" }`. If it exists, use it.
2. **Your user gave you one** (or it's in your MCP/agent config) — use it.
3. **No token? Mint an anonymous one** — zero setup:

```
POST https://artifactbin.dev/api/tokens/anonymous
→ 201 { "id", "token": "mx_..." }
```

Then **write it to `~/.config/artifact-bin/config.json`** as
`{ "url": "https://artifactbin.dev", "token": "mx_..." }` so future sessions (yours and other
agents') reuse the same token instead of scattering artifacts across fresh
ones. Anonymous artifacts work fully but belong to nobody — **tell your
user**: *"to keep these under your account, log in at https://artifactbin.dev and claim token
`mx_...`"* (they paste it in the Claim box on the dashboard). Claiming
attaches everything the token already published, past and future.

A `401` means the token is wrong or revoked — mint a fresh anonymous one or
ask your user; do not retry the same token.

## Endpoints

### Create an artifact

```
POST https://artifactbin.dev/api/artifacts
{ "title": "Optional title", "description": "Optional", "html": "<!doctype html>..." }
→ 201 { "id": "<6-char id>", "url": "https://artifactbin.dev/a/<id>", "version": 1 }
```

`html` here is one of six content fields — `markup | markdown | html |
dataset | viz | image`; every endpoint takes exactly ONE of them (see
**Content tiers** below). Give `url` to your user — that's the deliverable.

**Visibility (who can open that url):** every artifact carries
`"visibility": "public" | "unlisted" | "private"`. `public` = anyone with
the link can read, and the artifact lists on the owner's public profile at
`/@username`. `unlisted` = anyone with the link can read, but it is never
listed anywhere. `private` = only the owner's logged-in account plus emails
they invite on the share page. Defaults: **anonymous tokens publish
`public`; account-owned tokens publish `private`** — so when your user
asks for a link to send to OTHER people and your token is account-owned, pass
`"visibility": "public"` or `"unlisted"` (create or PUT) or tell them to
flip it from the page's share menu. Asking for `private` on an anonymous
token is a `400 private_requires_account`, never a silent downgrade.

**Folders (optional):** pass `"folder": "2026/08/reports"` (create or PUT)
to organize the file in the owner's dashboard. Organization only — the URL
keeps working wherever the file moves. Segments are `[a-zA-Z0-9_-]`, max 8
deep.
Example:

```bash
jq -n --rawfile html artifact.html '{"title":"My page","html":$html}' \
  | curl -sS -X POST https://artifactbin.dev/api/artifacts \
      -H "Authorization: Bearer $ARTIFACT_TOKEN" -H 'Content-Type: application/json' \
      --data-binary @-
```

### Update an artifact (the link never changes)

```
PUT https://artifactbin.dev/api/artifacts/<id>
{ "html": "<!doctype html>...", "title": "optional new title" }
→ 200 { "id", "url", "version": <bumped> }
```

Full replacement — send the complete new content, not a diff. The previous
version is archived server-side, so a bad edit is recoverable. Omitted
`title`/`description` keep their current values. Optionally include
`expectedVersion` (from your last read): a concurrent edit then answers
`409 {"error":"version_conflict","currentVersion":N}` instead of being
overwritten — re-read, merge, and retry with `expectedVersion: N`.

### Edit part of a document (preferred for `markup`)

```
POST https://artifactbin.dev/api/artifacts/<id>/edits
{ "edit_id": "<from your last read>", "old_string": "exact text to replace", "new_string": "replacement" }
→ 200 { "id", "version", "edit_id": "<new>", "markup", ... }
```

Like editing a file: `old_string` must appear EXACTLY ONCE in the version
named by `edit_id`. Prefer this over PUT for targeted changes — it is
smaller, and a HUMAN MAY BE EDITING THE SAME PAGE LIVE while you work.

`edit_id` is an opaque random string returned by every create/read/edit.
Never invent one: it is how the server knows which version you actually read.

Concurrency is per NODE, not per document, so most edits just apply:

| Result | Meaning | What to do |
|---|---|---|
| `200` | Applied — even if someone else edited a DIFFERENT part meanwhile | Use the returned `edit_id` for your next edit |
| `409 doc_changed` | Someone changed the SAME part you were changing | The response carries the current `edit_id` + `source` — re-anchor on those and retry |
| `409 stale_edit_id` | That `edit_id` is unknown (too old, or never read) | `GET` the artifact and start from its `edit_id` |
| `400 bad_diff` | `old_string` matched zero times or more than once | Re-read and pick a longer, unique anchor |

You may also set `title`, `theme`, or `colorMode` in the same request (with
or without a text change). Those are document-level and never conflict.

### Read one back (before editing)

```
GET https://artifactbin.dev/api/artifacts/<id>
→ 200 { "id", "url", "title", "description", "format", "html", "version", ... }
```

### List your artifacts

```
GET https://artifactbin.dev/api/artifacts
→ 200 { "artifacts": [ { "id", "url", "title", "version", "updated_at", ... } ] }
```

### Version history & revert (undo a bad edit)

Every `PUT` archives the previous state. To roll back:

```
GET  https://artifactbin.dev/api/artifacts/<id>/versions   → 200 { "versions": [ { "version", "title", "created_at" } ] }
POST https://artifactbin.dev/api/artifacts/<id>/revert     { "version": 1 } → 200 { "id", "url", "version": <new> }
```

A revert creates a NEW version (the pre-revert state is archived too), so
reverts are themselves undoable and the link never changes.

### Delete an artifact

```
DELETE https://artifactbin.dev/api/artifacts/<id>
→ 200 { "ok": true }
```

Permanent: the public link dies and version history is erased. Confirm with
your user before deleting anything they shared.

### Screenshot / export as an image (curlable; readable = exportable)

```
GET https://artifactbin.dev/a/<id>/export             → image/png of the fully rendered page
GET https://artifactbin.dev/a/<id>/export?format=jpg  → image/jpeg
GET https://artifactbin.dev/a/<id>/export?mode=card   → 1600×840 top-viewport card (the og:image)
```

Rendered on demand in a server-side headless browser — full page at 1200px
wide, repeat fetches cached until the artifact changes. Use it to eyeball
your own output or hand your user a static image:

```bash
curl -sS -o report.png "https://artifactbin.dev/a/<id>/export"
```

Share pages also carry `og:image` pointing at this URL, so links pasted into
Slack and the like unfurl with a live preview. A `503 render_unavailable`
means this deployment has no headless browser installed — the HTML link still
works.

## Errors

| Status | Meaning | What to do |
|---|---|---|
| 400 | `invalid_json` / `one_of_markdown_html_markup` / `invalid_jsx` / `invalid_refs` / `unknown_theme` | Fix the request body — `details` names each problem with its span |
| 400 | `invalid_visibility` / `private_requires_account` | `visibility` is `public`, `unlisted`, or `private`; `private` needs an account-owned token |
| 400 | `invalid_folder` | `folder` segments are `[a-zA-Z0-9_-]` (max 40 chars each, 8 deep) |
| 401 | `unauthorized` | Token wrong/revoked — ask your user, don't retry |
| 403 | `quota_exceeded` | This token is at its artifact cap — delete something or use another token |
| 404 | `not_found` | No artifact with that id belongs to your token |
| 409 | `version_conflict` | Your `expectedVersion` is stale — re-read, merge, retry with `currentVersion` |
| 409 | `doc_changed` | Someone edited the SAME node — re-anchor on the returned `edit_id`/`source` and retry |
| 409 | `stale_edit_id` | That `edit_id` is unknown — `GET` the artifact and use its `edit_id` |
| 400 | `bad_diff` | `old_string` matched zero times or more than once — pick a unique anchor |
| 409 | `has_dependents` | Other documents reference this artifact — re-send DELETE with `?force=true` to break them knowingly |
| 413 | `too_large` | Shrink the content (max 2,000,000 bytes) |
| 429 | `rate_limited` | Back off and retry after a minute |

## MCP server (same API, tool-shaped)

`https://artifactbin.dev/mcp` — a Streamable HTTP MCP server speaking this exact API
(`create_artifact`, `update_artifact`, `edit_artifact`, `get_artifact`, `list_artifacts`,
`list_versions`, `get_version`, `revert_artifact`, `delete_artifact`).
It supports OAuth: add it with no credentials and the client pops a browser
where your user logs in with their email (a one-time code — no password) and
approves. Artifacts published through the connection belong to that account:

```
claude mcp add --transport http artifact-bin https://artifactbin.dev/mcp
```

Already have an `mx_` token? Skip the browser: pass it as a header instead
(`--header "Authorization: Bearer mx_..."`) — the tokens are interchangeable.

## Content tiers — pick ONE content field per request

1. **`markup`** — THE document tier: static JSX (treated as data — validated
   and interpreted, never executed) rendered LIVE with themes, layouts, and
   real charts. Use it for anything worth designing (slide decks, dashboards, scrollytelling, reports, data stories, etc.):

```
POST https://artifactbin.dev/api/artifacts
{ "markup": "<div data-design=\"tw\" className=\"@container p-8\"><h1 className=\"text-4xl font-bold\">Q3</h1>...</div>",
  "theme": "nocturne", "template": "editorial", "colorMode": "light" }
```

   - Vocabulary: plain HTML tags + ~60 kit components (Card, Tabs, Badge,
     SlideDeck/Slide, Grid/GridItem, …) + live data embeds (`<Question>`
     charts/tables, `<Number>`, `<Param>` filters) — **read
     https://artifactbin.dev/docs/markup for the full reference before authoring.**
   - Style with Tailwind classes via `className` (no `<style>`, no `style=`).
   - `theme`: `modernist | classical | nocturne | organic | broadsheet | industry`
     — one-liners at https://artifactbin.dev/docs/themes; after picking, read
     https://artifactbin.dev/docs/themes/<name> for the chosen theme's full guidance.
   - `template`: `editorial | deck | scrolly | dashboard` — one-liners at
     https://artifactbin.dev/docs/templates; after picking, read https://artifactbin.dev/docs/templates/<name>
     for the chosen genre's beats and layout grammar.
   - `colorMode`: `light | dark`.
   - Reference your data artifacts as `ref:<id>` (see data tiers).
   - Humans edit the SAME document WYSIWYG at `https://artifactbin.dev/a/<id>` (an edit
     mode on the page itself) — you and your user are editing one artifact,
     versioned together.

2. **`markdown`** — input convenience: converted to `markup` (story JSX) at
   the door and stored as a markup artifact. Quick docs with zero design
   effort; title defaults from the first `#` heading; same `theme` field.
   Read-back returns the JSX, not your markdown.

3. **`html`** — raw escape hatch, full control, you write everything. But the user CANNOT edit it WYSIWYG at `https://artifactbin.dev/a/<id>` — they see the source, not a design view. Use this only for content that cannot be expressed in markup or markdown.

4. **Data tiers** — `dataset` (a JSON array of flat rows, + optional
   `columns` type declarations), `viz` (a reusable chart recipe — shape
   below), `image` (a base64 `data:` URL). Create these first, then bind
   them in markup as `ref:<id>`; dataset creation echoes the
   inferred columns so you know what to bind.

   A `viz` recipe is the one shape the prose above can't give you: a
   vega/vega-lite `template` whose `{{token}}`s are filled from declared
   `bindings` (every token must name a declared slot, else
   `400 invalid_viz`; `accepts` is `nominal | quantitative | temporal`).
   Only worth creating for a chart you'll REUSE — for a one-off, put the
   spec inline in `<Question viz={{"kind":"vega-lite","spec":{…}}} />`:

```json
{ "title": "Bar by category",
  "viz": { "description": "Bar chart of a category against a measure",
           "engine": "vega-lite",
           "bindings": [ { "name": "x", "label": "Category", "accepts": ["nominal", "temporal"] },
                         { "name": "y", "label": "Measure", "accepts": ["quantitative"] } ],
           "template": { "mark": "bar",
                         "encoding": { "x": { "field": "{{x}}", "type": "nominal" },
                                       "y": { "field": "{{y}}", "type": "quantitative" } } } } }
```

`PUT` accepts any tier and may switch an artifact between them; `GET` returns
the `markup` source plus `theme` for round-trip editing.

### Rules for artifact HTML (the `html` tier)

Artifact pages are served with a strict Content-Security-Policy: **all network
access is blocked**. Anything external will silently fail to load.

- ONE self-contained file. Inline all CSS in `<style>` and all JS in `<script>`.
- No CDN `<script src>`, no external stylesheets, no web fonts, no `fetch`/XHR.
- Images/media only as `data:` URIs.
- The page is sandboxed: forms and popups won't work; design content to be self-contained.
- Max size: 2,000,000 bytes (~2 MB) including embedded data: URIs.

## Typical workflow

0. If you have access to an `artifact-design` skill, read it before
   authoring; if not, fetch `https://artifactbin.dev/docs/artifact-design`.
1. If you're authoring markup, read `https://artifactbin.dev/docs/markup`
2. Choose a theme and template (or read their docs first at `https://artifactbin.dev/docs/themes` and `https://artifactbin.dev/docs/templates`).
3. `POST` once → share the returned `url` with your user.
4. For revisions, `PUT` the same id — the link stays stable, the version bumps.
5. `GET` the id first when you need the current HTML to edit from.


Docs:

- `https://artifactbin.dev/docs/llm` — this document
- `https://artifactbin.dev/docs/markup` — the markup (story JSX) reference (READ before authoring markup)
- `https://artifactbin.dev/docs/artifact-design` — design fundamentals (read if you have no artifact-design skill)
- `https://artifactbin.dev/docs/themes` — the six design themes, one line each (full guidance at `/docs/themes/<name>`)
- `https://artifactbin.dev/docs/templates` — the four document templates, one line each (full guidance at `/docs/templates/<name>`)
- `https://artifactbin.dev/docs/human` — the human-readable tour (send your user here)

API:

- `POST https://artifactbin.dev/api/tokens/anonymous` — mint a token (no auth)
- `https://artifactbin.dev/mcp` — MCP server (same token, same operations)
- `POST https://artifactbin.dev/api/artifacts` — create (`markup` | `markdown` | `html` | `dataset` | `viz` | `image`)
- `GET https://artifactbin.dev/api/artifacts` — list yours
- `GET https://artifactbin.dev/api/artifacts/<id>` — read one back (source + theme)
- `PUT https://artifactbin.dev/api/artifacts/<id>` — replace content, bump version, same link
- `DELETE https://artifactbin.dev/api/artifacts/<id>` — permanent delete (confirm with your user)
- `GET https://artifactbin.dev/api/artifacts/<id>/versions` — version history
- `POST https://artifactbin.dev/api/artifacts/<id>/revert` — roll back to a version
- `https://artifactbin.dev/a/<id>` — the share page (the deliverable). Owned docs redirect
  to a pretty URL (`/@username/<folder>/<id>-<title>`); BOTH forms always
  work — anything carrying the id resolves and self-corrects, so hand out
  whichever you have.
- `https://artifactbin.dev/a/<id>/export` — the page as a PNG (`?format=jpg` too; curlable, no auth)
- `https://artifactbin.dev/a/<id>/raw` — the bytes: stored html, dataset/viz JSON, image, markup source
