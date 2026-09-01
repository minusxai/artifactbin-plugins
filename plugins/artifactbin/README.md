# artifactbin plugin

Skills and MCP server for publishing self-contained HTML artifacts to https://artifactbin.dev.
This is the **production** release channel.

## Install (Claude Code)

```
/plugin marketplace add minusxai/artifactbin-plugins
/plugin install artifactbin@artifactbin
```

Local development from the artifactbin repository: `npm run build:plugin`,
then `claude --plugin-dir ./plugin`.

## What you get

- MCP tools (`create_artifact`, `edit_artifact`, and others) against `https://artifactbin.dev/mcp`.
- The `artifactbin` skill generated from the same files served by `https://artifactbin.dev/docs`.

## Self-hosting

This hosted-channel plugin points at https://artifactbin.dev. Self-hosters should generate
their own plugin from the source repository:

```
npm run build:plugin -- --base https://your-deployment.example
```

This directory is generated. Do not edit it directly.
