# Beta Workflows

## Unknown Workbook Recon

Use this order for large unknown spreadsheets:

1. `inspect_spreadsheet`
2. `read_range` for bounded header/sample ranges only
3. `analyze_range` for profiling and aggregates
4. `search_rows` for exact row lookup
5. Mutating tools only after target sheet, range, and operation are explicit

Treat `inspect_spreadsheet.classification` as evidence-based guidance, not
guaranteed business truth.

## Dedupe Before Append

IF appending a row that should be unique by email, ID, SKU, invoice number, or
similar key
  THEN call `search_rows` first.

IF `search_rows` finds an existing row
  THEN ask whether to update, skip, or append anyway.

## Safe Transform

1. Use `analyze_range` with the intended SQL.
2. Check result headers and row count.
3. Use `transform_range` with the same SQL.
4. If wrong, use `restore_snapshot(action: "list")` and then restore the chosen snapshot.

`transform_range` always fetches fresh data and writes results as `RAW`.

## Safe Row Delete

Use `delete_rows` instead of raw `batch_update_sheet` row deletes.

Preferred targets:

```json
{ "mode": "where_column_match", "header": "status", "value": "Cancelled" }
{ "mode": "row_numbers", "rowNumbers": [25, 26, 27] }
{ "mode": "a1_range", "range": "Orders!25:27" }
```

Do not pass cell-bounded A1 ranges such as `Orders!A25:C27` to row deletion.
Deleting rows removes entire rows.

## Formula Maintenance

IF the intent is to change a formula
  THEN let the formula guard preview show the existing and proposed formula.

IF only one substring should change
  THEN verify the preview shows only that intended change before passing an override.
