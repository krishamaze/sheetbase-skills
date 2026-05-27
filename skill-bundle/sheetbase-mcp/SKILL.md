---
name: sheetbase-mcp
description: Use when an external agent or user needs to operate the production Sheetbase MCP server, choose safe Google Sheets tools, recover from Sheetbase errors, or avoid unsafe spreadsheet reads and writes.
---

# Sheetbase MCP

IF task asks how to connect, authenticate, find spreadsheets, choose an initial workflow, or recover from auth errors
  THEN READ `references/using-sheetbase.md`.

IF task asks which Sheetbase tool to call, what parameters are required, or whether a requested tool is production-safe
  THEN READ `references/tool-contracts.md`.

IF task may write, append, clear, format, transform, delete, restore, or call raw batchUpdate
  THEN READ `references/safety.md`.

IF task mentions beta, `inspect_spreadsheet`, `search_rows`, `insert_column`, `move_column`, `delete_column`, or `delete_rows`
  THEN READ `references/release-channel.md`.
