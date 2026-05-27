# Using Beta Sheetbase

## Channel

This skill targets the beta Sheetbase MCP server and its 21-tool surface.

IF the user is connected to production Sheetbase
  THEN use `sheetbase-mcp` instead.

## First Move

IF the user provides no `spreadsheetId`
  THEN call `list_spreadsheets`.

IF the user provides a `spreadsheetId` but no tab name or `sheetId`
  THEN call `list_sheets`.

IF the workbook is unfamiliar OR the user asks for broad edits OR deletion
  THEN call `inspect_spreadsheet` before mutating.

IF using `format_cells`, `copy_sheet`, `manage_sheets`, or `batch_update_sheet`
  THEN get numeric `sheetId` with `list_sheets` first.

IF using A1 ranges
  THEN include the sheet/tab name in the range.

## Large Data

IF the task asks for aggregates, filtering, grouping, dedupe analysis, or profiling over hundreds of rows
  THEN prefer `analyze_range` over broad `read_range`.

IF the task needs exact row lookup by a header value
  THEN prefer `search_rows`.

IF `read_range` returns a truncation warning
  THEN switch to `search_rows`, `analyze_range`, or a narrower A1 range.

## Authentication Recovery

IF a response has `needsAuth: true`
  THEN tell the user to visit the Sheetbase dashboard and re-authorize Google.

IF the error says `No session found`
  THEN ask the user to reconnect the MCP client or verify the API key header.

IF the error says Google access token is missing
  THEN ask the user to sign out of the dashboard and connect Google again.
