# COMPLAINTS.json

## Purpose

Provides complaint inbox, complaint creation form, acknowledgement, and detailed complaint view.

## Entry and Flow Map

- initialPage: complaintInbox
- flows:
  - complaintInbox (TEMPLATE)
  - COMPLAINT_CREATE (FORM)
  - complaintAcknowledgement (TEMPLATE)
  - complaintView (TEMPLATE)

## How It Works

1. complaintInbox initializes with SEARCH_EVENT on pgrService.
2. Search/filter/sort popups update filters and trigger SEARCH_EVENT.
3. User can open complaintView for details or navigate to COMPLAINT_CREATE.
4. COMPLAINT_CREATE collects complaint type, location, complainant, and description.
5. Submit pipeline executes:
   - FETCH_TRANSFORMER_CONFIG (complaintRegistration)
   - CREATE_EVENT
   - NAVIGATION to complaintAcknowledgement
6. Acknowledgement returns user to complaintInbox.

## Major Actions Used

- SEARCH_EVENT, CLEAR_STATE, CLOSE_POPUP
- FETCH_TRANSFORMER_CONFIG, CREATE_EVENT
- NAVIGATION, BACK_NAVIGATION, SHOW_TOAST

## Important Form Rules

- otherReason shown only when complaintType is Other.
- complaintRaisedFor controls auto-fill of name and contact fields.
- contactNumber and supervisorContactNumber have strict mobile validations.
- locality captured through administrativeArea field.

## Major UI Formats Used

- actionPopup, listView, labelPairList, panelCard
- radio/radioList, dropdownTemplate, text/textArea, mobileNumber

## How To Use/Modify

- Add inbox filters: update complaintInbox popup body and SEARCH_EVENT data keys.
- Add complaint form field: append property in COMPLAINT_CREATE pages.
- Change acknowledgement routing: edit COMPLAINT_CREATE final NAVIGATION.

## Validation Checklist

- complaintRegistration transformer exists and maps all required fields.
- SEARCH_EVENT roots (pgrService, pgrComplainant, address) are valid.
- popUntilPageName values point to an existing flow name.

## Example Payload Snippets

```json
{
  "actionType": "SEARCH_EVENT",
  "properties": {
    "name": "pgrService",
    "data": [
      { "key": "serviceRequestId", "root": "pgrService", "value": "{{complaintNumber}}", "operation": "contains" },
      { "key": "mobileNumber", "root": "pgrComplainant", "value": "{{mobileNumber}}", "operation": "contains" }
    ]
  }
}
```

```json
{
  "actionType": "NAVIGATION",
  "properties": {
    "name": "complaintAcknowledgement",
    "type": "TEMPLATE",
    "navigationMode": "popUntilAndPush",
    "popUntilPageName": "complaintInbox",
    "data": [
      { "key": "complaintClientReferenceId", "value": "{{contextData.entities.PgrServiceModel.clientReferenceId}}" }
    ]
  }
}
```

## Troubleshooting

- Symptom: Search popup applies but list does not change.
  Cause: name/root in SEARCH_EVENT mismatched with wrapper search entity.
  Fix: Keep SEARCH_EVENT name as pgrService and validate root keys.
- Symptom: Complaint create succeeds but no acknowledgement data.
  Cause: clientReferenceId not present in contextData.entities path.
  Fix: Confirm transformer output and CREATE_EVENT entity binding.
- Symptom: Mobile number rejected unexpectedly.
  Cause: Validation pattern and length rules conflict with entered format.
  Fix: Match format mobileNumber with numeric-only 10-digit validation rules.
