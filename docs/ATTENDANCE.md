# ATTENDANCE.json

## Purpose

Handles attendance register listing, attendance marking, and success acknowledgement.

## Entry and Flow Map

- initialPage: manageAttendance
- flows:
  - manageAttendance (TEMPLATE)
  - markAttendance (TEMPLATE)
  - attendanceAcknowledgement (TEMPLATE)

## How It Works

1. manageAttendance loads attendance register data with SEARCH_EVENT.
2. wrapperConfig joins register + attendees + attendance logs into AttendanceWrapper.
3. User opens a register, navigating to markAttendance with registerId.
4. markAttendance loads the selected register and attendee details.
5. User marks present/absent, captures signature if required, and submits.
6. CREATE_EVENT stores AttendanceLogModel entries.
7. Navigation goes to attendanceAcknowledgement.

## Key Behaviors

- Supports search, filters, sorting, and grouped list rendering.
- Uses date/session controls to scope attendance entries.
- Uses actionPopup for final submit confirmation and comments.
- Includes QR-assisted attendance action.

## Major Actions Used

- SEARCH_EVENT, REFRESH_SEARCH
- CLEAR_STATE, CUSTOM_DATA
- CREATE_EVENT
- NAVIGATION, BACK_NAVIGATION, CLOSE_POPUP

## Major UI Formats Used

- listView, groupListView, card, labelPairList
- attendanceQRScannerButton, signatureCapture, signatureCompare
- actionPopup, selectButton, labeledToggle, date, searchBar

## How To Use/Modify

- Add a new register card field: update manageAttendance body.card.children.labelPairList data.
- Change submit logic: update markAttendance footer onAction order.
- Change attendance calculation: update called fn helpers used in value templates.
- Keep wrapperName and data references consistent (AttendanceWrapper, contextData paths).

## Validation Checklist

- registerId passed in navigation data.
- AttendanceLogModel creation succeeds on save/submit.
- Signature capture path matches individualId keys.
- Filter widget keys are cleared correctly by CLEAR_STATE.

## Example Payload Snippets

```json
{
  "actionType": "NAVIGATION",
  "properties": {
    "type": "TEMPLATE",
    "name": "markAttendance",
    "data": [
      { "key": "registerId", "value": "{{ item.AttendanceRegisterModel.id }}" },
      { "key": "entryTime", "value": "{{fn:entryTime()}}" }
    ]
  }
}
```

```json
{
  "actionType": "CREATE_EVENT",
  "properties": {
    "entity": "AttendanceLogModel"
  }
}
```

## Troubleshooting

- Symptom: Mark Attendance page opens blank.
  Cause: registerId was not passed in NAVIGATION data.
  Fix: Confirm markAttendance navigation includes registerId and SEARCH_EVENT uses navigation.registerId.
- Symptom: Submit button stays disabled.
  Cause: Widget-state selectors (attendanceCollection/signatureCollection) are not set as expected.
  Fix: Validate fn:anyAttendanceSelected/fn:allAttendanceSelected logic and widget key names.
- Symptom: Signature compare popup shows no baseline signature.
  Cause: Existing signature path does not match individualId/register references.
  Fix: Verify fn:getExistingSignature inputs and entity mapping keys.
