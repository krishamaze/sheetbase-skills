# Beta Tool Contracts

Beta Sheetbase exposes 21 tools.

## Read

| Tool | Use |
|---|---|
| `list_spreadsheets` | List Google Sheets files in Drive. |
| `list_sheets` | List tabs with numeric `sheetId`, title, and index. |
| `inspect_spreadsheet` | Read-only workbook recon across tabs, headers, formulas, classifications, and risks. |
| `read_range` | Read values from an explicit A1 range and receive `_schema`; header notes are included by default. |
| `search_rows` | Exact-match lookup within one column; returns rows with 1-indexed row numbers. |

## Write

| Tool | Use |
|---|---|
| `write_range` | Write a 2D values array to an exact A1 range. |
| `append_rows` | Append 2D row values to a table. |
| `clear_range` | Clear values while preserving formatting. |
| `format_cells` | Apply formatting by zero-based grid indexes and numeric `sheetId`. |

## SQL

| Tool | Use |
|---|---|
| `analyze_range` | Read-only SQL over sheet data. |
| `transform_range` | SQL transform that writes static results back in-place. |

## Manage

| Tool | Use |
|---|---|
| `create_spreadsheet` | Create a blank spreadsheet. |
| `manage_sheets` | Add, rename, or delete tabs. |
| `insert_column` | Insert a blank column before or after a safe `ColumnRef`. |
| `move_column` | Move a column before another column with formula and protection guards. |
| `delete_column` | Delete one column with confirmation and guards. |
| `delete_rows` | Delete rows using semantic targeting and required preview confirmation. |
| `copy_sheet` | Copy a tab to another spreadsheet. |
| `batch_update_sheet` | Raw Sheets API batchUpdate passthrough. |
| `restore_snapshot` | List or restore value-write snapshots. |

## Drive

| Tool | Use |
|---|---|
| `manage_drive` | Search Drive files or convert CSV/XLSX/TSV to Google Sheets. |

## Required Identifiers

IF a tool asks for `spreadsheetId`
  THEN pass the exact Google spreadsheet ID.

IF a tool asks for `sheetName`
  THEN pass the exact tab title.

IF a tool asks for `sheetId`
  THEN pass the numeric sheet ID from `list_sheets`.

IF a tool asks for `range`
  THEN pass explicit A1 notation with a tab prefix, such as `Orders!A1:F100`.

## ColumnRef

Column tools accept safe column references:

```json
{ "mode": "header", "value": "Order ID" }
{ "mode": "letter", "value": "C", "expectedHeader": "Status" }
{ "mode": "index", "value": 2 }
```

Prefer `mode: "header"` when headers are unique. Use `expectedHeader` as a
drift check for letter or index targeting.

## SQL Model

`analyze_range` and `transform_range` expose rows as:

```sql
SELECT ... FROM sheet_data
```

Each row is a JSONB object named `data`. All values are strings, so cast before
math:

```sql
SUM((data ->>'Revenue')::numeric)
```

Use exact header keys unless `_schema.sqlHeaders[].queryableAliases` lists a
safe alias.
