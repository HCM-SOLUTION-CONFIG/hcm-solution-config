# INVENTORY.json

## Purpose

Implements stock management, stock receipt/issue/return flows, transaction viewing, and stock success acknowledgement.

## Entry and Flow Map

- initialPage: manageStock
- flows:
  - manageStock (TEMPLATE)
  - returnOrIssueSelection (TEMPLATE)
  - scanStockReceipt (TEMPLATE)
  - RECORDSTOCK (FORM)
  - RECORDLESSEXCESS (FORM)
  - stockSuccess (TEMPLATE)
  - viewTransaction (TEMPLATE)
  - viewTransactionDetails (TEMPLATE)
  - incomingTransactions (TEMPLATE)
  - stockReceiptDetails (FORM)

## How It Works

1. manageStock loads projectFacility inventory context.
2. User selects stock action path (receipt, issue, return, transaction view).
3. returnOrIssueSelection decides navigation data based on transactionType.
4. scanStockReceipt can scan MRN, search stock, reverse-transform, then open RECORDSTOCK.
5. RECORDSTOCK multi-page form captures warehouse, stock, and product-level details.
6. Submit pipeline runs:
   - FETCH_TRANSFORMER_CONFIG (stock)
   - CREATE_EVENT
   - UPDATE_STOCK_BALANCE
   - NAVIGATION to stockSuccess

## Major Actions Used

- SEARCH_EVENT, OPEN_SCANNER, REVERSE_TRANSFORM
- FETCH_TRANSFORMER_CONFIG, CREATE_EVENT, UPDATE_EVENT
- UPDATE_STOCK_BALANCE
- NAVIGATION, BACK_NAVIGATION, SHOW_TOAST

## Key Data/Validation Logic

- facility hierarchy validation enforces allowed from/to facility chains.
- many quantity fields are conditionally shown by stockEntryType.
- scanResource includes comparisonConfig to prevent duplicate scans.
- product-level entry supports multi-entity processing.

## Major UI Formats Used

- menu_card, listView, labelPairList, panelCard
- dropdownTemplate/dropdown, scanner/qrScanner
- card, row, column, text, textArea, actionPopup

## How To Use/Modify

- Add stock type path: update returnOrIssueSelection enums and conditional NAVIGATION actions.
- Add stock form field: update RECORDSTOCK page property + transformer mapping.
- Adjust balance behavior: update UPDATE_STOCK_BALANCE action and transformer payload.

## Validation Checklist

- stock transformer accepts all navigation/form keys.
- stockEntryType-specific validations match field visibility conditions.
- entity/search names used in wrapperConfig and actions are consistent.
- popUntilPageName references existing flow names.

## Example Payload Snippets

```json
{
  "actionType": "FETCH_TRANSFORMER_CONFIG",
  "properties": {
    "configName": "stock",
    "data": [
      { "key": "stockEntryType", "value": "{{navigation.stockEntryType}}" },
      { "key": "transactionType", "value": "{{navigation.transactionType}}" },
      { "key": "mrnNumber", "value": "{{navigation.mrnNumber}}" }
    ]
  }
}
```

```json
{
  "actionType": "UPDATE_STOCK_BALANCE",
  "properties": {
    "entity": "STOCK"
  }
}
```

## Troubleshooting

- Symptom: RECORDSTOCK submit fails after transform.
  Cause: Required navigation keys (stockEntryType, transactionType, role keys) missing.
  Fix: Validate every entry path leading to RECORDSTOCK sets required NAVIGATION data.
- Symptom: Duplicate resource scan accepted.
  Cause: comparisonConfig filters do not match persisted stock keys.
  Fix: Align comparisonConfig filter keys with saved stock payload structure.
- Symptom: Facility selection validation blocks all options.
  Cause: facilityHierarchy mapping does not match current facility levels/labels.
  Fix: Review hierarchyMapping nodes and role-specific flow path.
