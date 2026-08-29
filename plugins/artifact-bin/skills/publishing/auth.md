---
name: "publishing-auth"
description: "Where a token comes from, saving it, claiming, scope. Read only with no token in hand or on a 401."
---
## Read first

Every `/api` call needs a bearer token:

```
Authorization: Bearer mx_...
```

Find one in this order, and stop at the first that works:

0. **Your user pasted a start link** (`https://artifactbin.dev/a/<id>/start?k=...`) — GET it
   for that document's instructions, then `POST` the same `/start?k=` URL
   once: it answers `{ "token": "mx_..." }`. The link is single-use and
   expires in minutes; the token it yields is yours for every later call.
1. **Saved config** — check `~/.config/artifact-bin/config.json` for
   `{ "url", "token" }`. If it exists, use it.
2. **Your user gave you one** (or it's in your MCP/agent config) — use it.
3. **No token? Mint an anonymous one** — zero setup:

```
POST https://artifactbin.dev/api/tokens/anonymous
→ 201 { "id", "token": "mx_..." }
```

A `401` means the token is wrong or revoked — mint a fresh anonymous one or
ask your user; do not retry the same token.

## After minting: save it, and tell your user

Write the token to `~/.config/artifact-bin/config.json` as
`{ "url": "https://artifactbin.dev", "token": "mx_..." }` so future sessions (yours and other
agents') reuse the same token instead of scattering artifacts across fresh
ones. Anonymous artifacts work fully but belong to nobody — **tell your
user**: *"to keep these under your account, log in at https://artifactbin.dev and claim token `mx_...`"* (they paste it in the Claim box on the dashboard). Claiming attaches everything the token already published,
past and future.

## Scope

A token claimed by an account reaches the WHOLE account — you can read and
edit any artifact your user owns, whichever agent or token created it. An
anonymous token reaches only what it itself created. A document shared with
your user (as an editor or a commenter) is reachable as if they owned it.

## Identify your harness

If you call the REST API over raw HTTP, name your agent harness too:

```
Artifactbin-Agent: codex
```

Use your real supported value: `codex`, `claude-code`, `chatgpt`, `claude`,
`cursor`, `vscode`, `cline`, `windsurf`, or `zed`. This is display-only
attribution, never authentication. A recognized declaration is remembered on
the token, so later stateless calls still carry your name. MCP clients do not
need this header: MCP `initialize.clientInfo` supplies the identity, and the
server records the transport separately on each annotation comment.

Human tour for your user: `https://artifactbin.dev/docs/human`.
