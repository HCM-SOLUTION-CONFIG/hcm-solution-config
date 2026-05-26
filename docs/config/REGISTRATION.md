# REGISTRATION.json

## Purpose

Defines the largest beneficiary workflow: search, household overview, registration, delivery, redose, referral handoff, and acknowledgements.

## Entry and Flow Map

- initialPage: searchBeneficiary
- flows (15):
  - deliverySuccess (TEMPLATE)
  - redoseSuccess (TEMPLATE)
  - beneficiaryDetails (TEMPLATE)
  - referralSuccess (TEMPLATE)
  - householdOverview (TEMPLATE)
  - UNABLETODELIVER (FORM)
  - searchBeneficiary (TEMPLATE)
  - DELIVERY (FORM)
  - REDOSE (FORM)
  - CHECKLIST (FORM)
  - ADD_MEMBER (FORM)
  - ACKNOWLEDGEMENT (TEMPLATE)
  - REFER_BENEFICIARY (FORM)
  - HOUSEHOLD (FORM)
  - caregiverAcknowledgement (TEMPLATE)

## How It Works

1. searchBeneficiary and related searches identify household/member context.
2. householdOverview builds a rich joined model (household, members, individual, tasks, referral, side effects).
3. Visibility logic decides whether user can deliver, redose, refer, or only view status.
4. Delivery and redose forms run transformer + create/update + stock updates.
5. Success/acknowledgement templates route users back to household or search.

## Major Actions Used

- SEARCH_EVENT, REFRESH_SEARCH, OPEN_SCANNER
- REVERSE_TRANSFORM, FETCH_TRANSFORMER_CONFIG
- CREATE_EVENT, UPDATE_EVENT
- UPDATE_STOCK_BALANCE, UPDATE_IDENTIFIER_STATUS
- NAVIGATION, BACK_NAVIGATION, CLOSE_POPUP, CLEAR_STATE
- LOAD_UNIQUE_ID_POOL, NAVIGATE_TO_BENEFICIARY_ID_DOWN_SYNC

## Core Technical Patterns

- Heavy wrapperConfig use with relations, computed, computedList, and filters.
- Eligibility and next-dose logic derived through computed expressions.
- Many actions depend on navigation payload continuity across pages.
- Form flows and template flows are tightly coupled by shared IDs and references.

## Major UI Formats Used

- searchBar/proximitySearch, listView, table, expandable
- selectionCard, actionPopup, panelCard
- scanner/qrScanner, dropdown, radio, switch, text/textArea, tags

## How To Use/Modify Safely

- Change one flow at a time and verify cross-flow navigation keys.
- Keep computed field names stable if referenced by visibility expressions.
- When adding delivery/redose fields, update transformer config and stock update logic together.
- Validate all popUntilPageName targets after any rename.

## Validation Checklist

- search wrapper names and entity names are consistent.
- all navigation keys used in destination screens are always provided.
- stock-related actions run only when required payload fields exist.
- required validations align with visibility conditions.

## Example Payload Snippets

```json
{
  "actionType": "NAVIGATION",
  "properties": {
    "name": "DELIVERY",
    "type": "FORM",
    "data": [
      { "key": "ProjectBeneficiaryClientReferenceId", "value": "{{projectBeneficiaries.0.clientReferenceId}}" },
      { "key": "HouseholdClientReferenceId", "value": "{{household.0.clientReferenceId}}" },
      { "key": "cycleIndex", "value": "{{contextData.0.nextCycleId}}" },
      { "key": "doseIndex", "value": "{{contextData.0.nextDoseId}}" }
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

- Symptom: Household overview buttons show wrong eligibility state.
  Cause: computed/computedList dependencies changed (cycle, dose, referral references).
  Fix: Validate wrapper computed order and source paths after edits.
- Symptom: Delivery/Redose submit fails after form completion.
  Cause: Missing required navigation payload keys between screens.
  Fix: Trace NAVIGATION data from householdOverview -> checklist/delivery/redose flows.
- Symptom: Unique beneficiary ID flow stalls.
  Cause: LOAD_UNIQUE_ID_POOL or DOWN_SYNC action conditions not satisfied.
  Fix: Verify identifier pool availability and action conditions in ADD_MEMBER flow.
