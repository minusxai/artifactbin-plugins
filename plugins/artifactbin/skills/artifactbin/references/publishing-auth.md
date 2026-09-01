---
name: "publishing-auth"
description: "Where a token comes from, saving it, claiming, scope. Read only with no token in hand or on a 401."
---
## Read first

**Agent token contract**

Check it first: `~/.artifactbin.env` stores the current connection as:

```dotenv
ARTIFACTBIN_URL=https://artifactbin.dev
ARTIFACTBIN_TOKEN=<token>
```

If a token is there, use it. Store every newly received token there; the most recent token wins. As a legacy fallback, also read `~/.config/artifact-bin/config.json` with `{ "url", "token" }`.

If your user's paste says `using this token: mx_…`, that IS your token: save it to `~/.artifactbin.env` and use it.

There are two ways to get a token: the human can paste a start link, which you POST once for `{ "token", "expiresAt" }`, or you can send the human to https://artifactbin.dev/tokens/new and ask them to paste the new token back. Tokens expire after 6 h by default; `expiresInHours` may be 1–720 at mint, and `expiresAt` says exactly when.

With no token, or after a `401` or expired token, do not retry blindly. Send the human to https://artifactbin.dev/tokens/new, save the replacement token, and resume the interrupted work. Anonymous tokens can be claimed by an account on https://artifactbin.dev/ or rejected there.

Never put a token in a URL or commit it. Send it only in the `Authorization: Bearer <token>` header.

Without a human in the loop, `POST https://artifactbin.dev/api/tokens/anonymous` mints an anonymous token. After publishing, tell your user: *"to keep these under your account, log in at https://artifactbin.dev and claim token `mx_...`"* (they paste it in the Claim box on the dashboard).

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
