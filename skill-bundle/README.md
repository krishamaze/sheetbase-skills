# Sheetbase Skill Bundle

This directory is the canonical private source for Sheetbase external-user skills.

Published mirror:

```text
sheetbase-skills/
  skill-bundle/
    sheetbase-mcp/
    sheetbase-mcp-beta/
```

Internal development instructions stay in `AGENTS.md`, `CLAUDE.md`, and `GEMINI.md`.
Installed local skill dependencies stay under `.agents/` and are not source of truth.

## Skills

- `sheetbase-mcp`: production/stable Sheetbase MCP tool surface.
- `sheetbase-mcp-beta`: beta Sheetbase MCP tool surface.

## Maintenance Rule

Any PR that changes MCP tools, tool schemas, safety behavior, authentication behavior,
or public docs must answer:

```text
Skill impact:
- sheetbase-mcp updated: yes/no
- sheetbase-mcp-beta updated: yes/no
- Public mirror needed: yes/no
```

See `PUBLISHING.md` for mirror instructions.
