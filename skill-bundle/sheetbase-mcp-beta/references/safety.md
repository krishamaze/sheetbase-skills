# Beta Safety Rules

## Default Order

IF workbook structure is unknown
  THEN call `inspect_spreadsheet` before broad reads, transforms, deletes, or raw batch updates.

IF the task can be answered with SQL
  THEN use `analyze_range` instead of reading thousands of rows into context.

IF writing to existing data
  THEN read the target range or relevant headers first and inspect `_schema`.

## Metadata-Aware Reads And Writes

`read_range` includes header notes by default in `_schema.columns`. Structured
note prefixes become `noteGuard` metadata:

- `[INFO]`
- `[WARN]`
- `[FORMULA]`
- `[LOCK]`

Blocking note guards stop writes unless `allowNoteGuardOverride: true` is passed.
Do not pass that override until the note is understood.

## Formula Guards

`write_range`, `append_rows`, and `transform_range` block formula damage by
default. Formula overwrite blocks include existing formula, proposed value, and
a small diff preview.

IF a write returns formula overwrite previews
  THEN compare the existing formula and proposed value before retrying with `allowFormulaOverwrite: true`.

IF `_schema.arrayFormulaColumns` or `_schema.warnings` mention an ARRAYFORMULA
  THEN avoid that column unless formula replacement is intentional.

## Structural Edits

Prefer focused tools over raw `batch_update_sheet`:

- `insert_column`
- `move_column`
- `delete_column`
- `delete_rows`

Use raw `batch_update_sheet` only when no focused tool fits.

## Destructive Row Deletes

`delete_rows` is two-phase:

1. Preview call without `confirmDelete`.
2. Confirm call with `confirmDelete: true`, `previewToken`, `expectedRowNumbers`, and `expectedRowCount`.

Do not use raw zero-based row indexes for row deletion when `delete_rows` can do
the job. `delete_rows` uses 1-based row numbers, row-only A1 ranges, or
`where_column_match`.

Row deletion cannot be restored automatically. `delete_rows_audit` snapshots are
for forensic review only.

## Snapshots

`write_range` and `transform_range` save pre-write snapshots when the range is
small enough. Use `restore_snapshot(action: "list")` before restore.

Do not promise undo for row deletion, tab deletion, raw batch updates, or
oversized writes.
