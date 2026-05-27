# Production Safety Rules

## Default Order

IF workbook structure is unknown
  THEN inspect small headers/samples before mutating.

IF the task can be answered with SQL
  THEN use `analyze_range` instead of reading thousands of rows into context.

IF writing to existing data
  THEN read the target range or relevant headers first and inspect `_schema`.

## Write Guards

`write_range`, `append_rows`, and `transform_range` run formula and ARRAYFORMULA
safety checks by default. Do not pass `allowFormulaOverwrite: true` until the
user or task has confirmed the overwrite or spill is intentional.

IF a write reports formula cells in the target range
  THEN inspect the target formulas with a bounded read before retrying with an override.

IF `_schema.arrayFormulaColumns` or `_schema.warnings` mention an ARRAYFORMULA
  THEN avoid that column unless formula replacement is intentional.

IF a protected range is hard-locked
  THEN do not retry with overrides; ask the user to unlock or choose another range.

## Destructive Actions

`manage_sheets` delete requires `confirmDelete: true` for non-empty sheets. This
deletes an entire tab and cannot be restored with `restore_snapshot`.

`batch_update_sheet` is raw API passthrough. Use it only when no focused tool
fits, and prefer small explicit requests with numeric `sheetId` values from
`list_sheets`.

## Snapshots

`write_range` and `transform_range` save pre-write snapshots when the range is
small enough. Use `restore_snapshot(action: "list")` before restore.

Do not promise undo for tab deletion, raw batch updates, or oversized writes.
