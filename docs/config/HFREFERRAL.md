# HFREFERRAL.json

## Purpose

Manages health facility referral lifecycle: inbox, referral overview, create/edit referral form, and acknowledgement.

## Entry and Flow Map

- initialPage: referralInbox
- flows:
  - referralOverview (TEMPLATE)
  - referralAcknowledgement (TEMPLATE)
  - referralInbox (TEMPLATE)
  - REFERRAL_CREATE (FORM)

## How It Works

1. referralInbox lists referrals and supports search, QR scan, and pagination refresh.
2. Open action navigates to referralOverview with clientReferenceId.
3. referralOverview loads referral by SEARCH_EVENT and shows computed status.
4. Continue path reverse-transforms data and opens REFERRAL_CREATE in edit mode.
5. REFERRAL_CREATE pages capture referral details and side-effect questionnaires.
6. Submit pipeline (per page/footer) runs:
   - optional SEARCH_EVENT for edit mode
   - FETCH_TRANSFORMER_CONFIG (referralCreation)
   - CREATE_EVENT or UPDATE_EVENT
   - NAVIGATION to referralAcknowledgement

## Major Actions Used

- SEARCH_EVENT, REFRESH_SEARCH
- OPEN_SCANNER
- REVERSE_TRANSFORM
- FETCH_TRANSFORMER_CONFIG, CREATE_EVENT, UPDATE_EVENT
- NAVIGATION, BACK_NAVIGATION, SHOW_TOAST

## Notable Design Patterns

- Edit/new behavior controlled by navigation.isEdit.
- Conditional fields use visibilityCondition expressions.
- List paging handled by scrollListener and REFRESH_SEARCH.
- Reusable submit pipeline appears in multiple page footers.

## Major UI Formats Used

- listView, infoCard, card, panelCard
- searchBar, qrScanner, radio, dropdown, date, custom

## How To Use/Modify

- Add referral question: update REFERRAL_CREATE page properties.
- Change status logic: update function usage in labelPairList values.
- Adjust paging: edit wrapperConfig.searchConfig.pagination and scrollListener.

## Validation Checklist

- hFReferral wrapper name and search name stay consistent.
- referralCreation transformer supports both create and update payloads.
- navigation keys used in REFERRAL_CREATE are always passed by prior screens.

## Example Payload Snippets

```json
{
  "actionType": "REVERSE_TRANSFORM",
  "properties": {
    "configName": "referralCreation",
    "entityTypes": ["HFReferralModel"]
  }
}
```

```json
{
  "actionType": "UPDATE_EVENT",
  "properties": {
    "applyIf": "navigation.isEdit==true"
  }
}
```

## Troubleshooting

- Symptom: Edit referral opens empty form.
  Cause: REVERSE_TRANSFORM ran without source entities or wrong clientReferenceId.
  Fix: Ensure referralOverview passes clientReferenceId and search returns HFReferralModel.
- Symptom: Create works, edit fails silently.
  Cause: applyIf logic for CREATE_EVENT/UPDATE_EVENT is incorrect.
  Fix: Verify navigation.isEdit is set as boolean-like value expected by expressions.
- Symptom: Infinite list scroll does not load more records.
  Cause: REFRESH_SEARCH pagination or wrapper search name mismatch.
  Fix: Confirm wrapperName/search primary is hFReferral and pagination keys are valid.
