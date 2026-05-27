# Beta Workflows

## Unknown Workbook Recon

Use Sheetbase like a spreadsheet forensic analyst: understand structure first,
business meaning second, transformations last.

IF no spreadsheet ID, Sheetbase link, or accessible workbook is available
  THEN ask for the missing workbook access and stop.

IF a spreadsheet ID or Sheetbase link is available
  THEN do not ask broad intake questions before recon.

Disallowed before recon:

- "What is this spreadsheet about?"
- "What do you want to do in the sheet?"
- business-context questions not tied to observed sheet names, columns, formulas,
  ranges, or ambiguous values

Use this order for large unknown spreadsheets:

1. `inspect_spreadsheet`
2. `read_range` for bounded header/sample ranges only
3. `analyze_range` for profiling and aggregates
4. `search_rows` for exact row lookup
5. Mutating tools only after target sheet, range, and operation are explicit

Treat `inspect_spreadsheet.classification` as evidence-based guidance, not
guaranteed business truth.

For each important tab, report sheet name, approximate rows and columns, likely
type, formula presence, protected or locked constraints when visible, and notable
artifacts such as filters, charts, pivots, named ranges, or frozen panes.

Classify tabs with evidence:

| Type | Evidence |
|---|---|
| Transaction | Many rows, repeated structure, dates, amounts, IDs |
| Master/reference | Fewer rows, unique IDs, lookup-style columns |
| Calculation | Dense formulas, helper columns, intermediate tables |
| Reporting/dashboard | Charts, pivots, KPIs, summary ranges |
| Config | Small constants, thresholds, dropdown sources |

Ask questions only after recon, and make them precise:

```text
I observed [sheet/range/column/value/formula]. It suggests [interpretation], but
[specific ambiguity] is unsafe to infer. Should I treat [A] as [meaning 1] or
[meaning 2]?
```

For unknown workbook deliverables, use a concise recon report:

```text
Architecture map
- Orders | Rows: ~52k | Cols: 34 | Type: transaction | Evidence: dates, order IDs, repeated SKU values

Relationships
- Orders.sku -> Master_SKU.sku_code | Confidence: medium | Needs value-overlap check

Formula risks
- Summary!B2:B uses SUMIFS over Orders. Do not overwrite Summary formulas.

Data quality risks
- Orders.created_at has mixed date formats in sampled rows.

Questions
- Does status `C` mean cancelled or completed?
```

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
