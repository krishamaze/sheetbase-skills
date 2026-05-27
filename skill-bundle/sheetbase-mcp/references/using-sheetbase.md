# Using Production Sheetbase

## Channel

This skill targets production Sheetbase. Production currently exposes the stable
tool surface documented in `tool-contracts.md`.

IF the user is connected to a beta Sheetbase server
  THEN use `sheetbase-mcp-beta` instead.

## First Move

IF the user provides no `spreadsheetId`
  THEN call `list_spreadsheets`.

IF the user provides a `spreadsheetId` but no tab name or `sheetId`
  THEN call `list_sheets`.

IF using `format_cells`, `copy_sheet`, `manage_sheets`, or `batch_update_sheet`
  THEN get numeric `sheetId` with `list_sheets` first.

IF using A1 ranges
  THEN include the sheet/tab name in the range.

## Large Data

IF the task asks for aggregates, filtering, grouping, dedupe analysis, or profiling over hundreds of rows
  THEN prefer `analyze_range` over broad `read_range`.

IF `read_range` returns a truncation warning
  THEN switch to `analyze_range` with a narrower query or A1 range.

## Unknown Workbooks

Production does not include the beta `inspect_spreadsheet` recon tool. For an
unknown workbook, still use a recon-first workflow.

IF no spreadsheet ID, Sheetbase link, or accessible workbook is available
  THEN ask for the missing workbook access and stop.

IF a spreadsheet ID or Sheetbase link is available
  THEN do not ask broad intake questions before recon.

Disallowed before recon:

- "What is this spreadsheet about?"
- "What do you want to do in the sheet?"
- business-context questions not tied to observed sheet names, columns, formulas,
  ranges, or ambiguous values

Production fallback order:

1. Call `list_sheets`.
2. Read headers and small samples with bounded `read_range` calls.
3. Use `analyze_range` for large profiling.
4. Ask precise questions only for ambiguous business codes or destructive edits.

State conclusions with evidence and confidence instead of guessing business
meaning from the file name alone.

## Authentication Recovery

IF a response has `needsAuth: true`
  THEN tell the user to visit the Sheetbase dashboard and re-authorize Google.

IF the error says `No session found`
  THEN ask the user to reconnect the MCP client or verify the API key header.

IF the error says Google access token is missing
  THEN ask the user to sign out of the dashboard and connect Google again.
