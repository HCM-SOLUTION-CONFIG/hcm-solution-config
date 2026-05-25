# STOCKREPORTS.json

## Purpose

Provides stock report navigation and detail views for issue/return/receipt and reconciliation report types.

## Entry and Flow Map

- initialPage: viewReports
- flows:
  - viewReports (TEMPLATE)
  - reportDetails (TEMPLATE)
  - reckonReportDetails (TEMPLATE)

## How It Works

1. viewReports loads project facility/product context through SEARCH_EVENT.
2. User selects report menu card (issued, returned, receipt, reconciliation).
3. Navigation passes reportType, facilities, and productVariants.
4. reportDetails/reckonReportDetails render filter dropdowns and trigger SEARCH_EVENT.
5. Table views display filtered records; info cards handle empty/default states.

## Major Actions Used

- SEARCH_EVENT
- NAVIGATION, BACK_NAVIGATION

## Major UI Formats Used

- menu_card
- dropdownTemplate
- table
- infoCard
- card, button, backLink

## Key Data Logic

- Uses helper functions to map reportType into sender/receiver and stock entry type filters.
- Facility dropdown visibility depends on user role.
- Product/facility selections trigger stock query refresh.

## How To Use/Modify

- Add report type: add menu card in viewReports and map reportType helper behavior.
- Change report columns: update table data.columns in detail flow.
- Change filter behavior: update SEARCH_EVENT data keys in dropdown onChange actions.

## Validation Checklist

- reportType values are supported by helper functions.
- selectedFacility and selectedProduct keys are correctly set by dropdowns.
- StockModel/stockReconciliation table rows read from the correct context path.

## Example Payload Snippets

```json
{
  "actionType": "NAVIGATION",
  "properties": {
    "type": "TEMPLATE",
    "name": "reportDetails",
    "data": [
      { "key": "reportType", "value": "dispatch" },
      { "key": "facilities", "value": "{{fn:getCurrentFacilities(ProjectFacilityModel)}}" },
      { "key": "productVariants", "value": "{{ProductVariantModel}}" }
    ]
  }
}
```

```json
{
  "actionType": "SEARCH_EVENT",
  "properties": {
    "name": "stock",
    "data": [
      { "key": "tenantId", "value": "{{singleton.selectedProject.tenantId}}", "operation": "equals" },
      { "key": "productVariantId", "value": "{{selectedProduct}}", "operation": "equals" }
    ]
  }
}
```

## Troubleshooting

- Symptom: Report table always empty after filter selection.
  Cause: reportType mapping helper returns mismatched stock entry type.
  Fix: Validate fn:getStockEntryType and fn:getSenderOrReceiver outputs.
- Symptom: Facility dropdown hidden for expected users.
  Cause: hasRole expression does not match actual user role code.
  Fix: Confirm role string in visibility condition and auth payload.
- Symptom: Back to home button returns to wrong screen stack.
  Cause: BACK_NAVIGATION properties mismatch intended HOME target.
  Fix: Keep type/name set to HOME when leaving report details.
