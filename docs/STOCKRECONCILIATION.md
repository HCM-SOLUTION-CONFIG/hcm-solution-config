# STOCKRECONCILIATION.json

## Purpose

Captures manual stock reconciliation for selected facility/product context and creates reconciliation records.

## Entry and Flow Map

- initialPage: stockReconciliationDetails
- flows:
  - stockReconciliationDetails (FORM)
  - stockReconciliationSuccess (TEMPLATE)

## How It Works

1. initActions SEARCH_EVENT loads projectFacility context.
2. wrapperConfig joins project facility with facility/product variant/stock.
3. User fills stockRecon page:
   - stockReconciliationCard (custom dynamic selector)
   - manualCount (required, min 0)
   - stockInHand (hidden, read-only reference)
   - comments (required when mismatch condition is true)
4. Submit runs:
   - FETCH_TRANSFORMER_CONFIG (stockReconciliation)
   - CREATE_EVENT (StockReconciliationModel)
   - NAVIGATION to stockReconciliationSuccess

## Major Actions Used

- SEARCH_EVENT
- FETCH_TRANSFORMER_CONFIG
- CREATE_EVENT
- NAVIGATION, SHOW_TOAST

## Major UI Formats Used

- custom
- number
- textArea
- panelCard

## How To Use/Modify

- Add reconciliation dimensions: expand stockRecon properties and transformer mapping.
- Tighten mismatch behavior: update comments visibilityCondition expression.
- Change post-submit path: edit final NAVIGATION target.

## Validation Checklist

- stockReconciliation transformer exists.
- StockReconciliationModel entity repository is enabled.
- manualCount and stockInHand path names match expression references.

## Example Payload Snippets

```json
{
   "actionType": "SEARCH_EVENT",
   "properties": {
      "name": "projectFacility",
      "data": [
         { "key": "projectId", "value": "{{singleton.selectedProject.id}}", "operation": "equals" }
      ]
   }
}
```

```json
{
   "actionType": "CREATE_EVENT",
   "properties": {
      "entity": "StockReconciliationModel"
   }
}
```

## Troubleshooting

- Symptom: comments field never appears on mismatch.
   Cause: visibilityCondition path does not match page key/field names.
   Fix: Verify expression uses stockRecon.manualCount and stockRecon.stockInHand.
- Symptom: Submit creates no record but no visible error.
   Cause: Transformer returned empty/invalid entity payload.
   Fix: Inspect stockReconciliation transformer output for required fields.
- Symptom: Product/facility options are empty.
   Cause: projectFacility SEARCH_EVENT missing selected project context.
   Fix: Ensure singleton.selectedProject.id is available before opening screen.
