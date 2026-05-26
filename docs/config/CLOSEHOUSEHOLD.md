# CLOSEHOUSEHOLD.json

## Purpose

Captures household closure details and creates closure-related entities.

## Entry and Flow Map

- initialPage: CLOSEHOUSEHOLD
- flows:
  - CLOSEHOUSEHOLD (FORM)
  - closeHouseholdSuccess (TEMPLATE)

## How It Works

1. User fills closeHouseholdDetails page fields:
   - administrativeArea (locality)
   - latLng
   - headName
   - scanner (hidden support field)
2. On submit, onAction pipeline runs:
   - FETCH_TRANSFORMER_CONFIG with closeHouseholdRegistration
   - CREATE_EVENT for HOUSEHOLD, INDIVIDUAL, MEMBER, PROJECTBENEFICIARY
   - NAVIGATION to closeHouseholdSuccess
3. Success screen shows panel card and returns home.

## Major Actions Used

- FETCH_TRANSFORMER_CONFIG
- CREATE_EVENT
- NAVIGATION, SHOW_TOAST

## Key Validation Rules

- administrativeArea required
- latLng required
- headName minLength

## How To Use/Modify

- Add new closure input: append page property in closeHouseholdDetails.
- Change output entity mapping: update transformer configName and mapped transformer file.
- Adjust success destination: modify closeHouseholdSuccess primaryAction onAction.

## Validation Checklist

- Transformer config closeHouseholdRegistration exists.
- CREATE_EVENT entity list matches available repositories.
- Required fields are not hidden if still required.

## Example Payload Snippets

```json
{
   "actionType": "FETCH_TRANSFORMER_CONFIG",
   "properties": {
      "configName": "closeHouseholdRegistration",
      "data": []
   }
}
```

```json
{
   "actionType": "CREATE_EVENT",
   "properties": {
      "entity": "HOUSEHOLD, INDIVIDUAL, MEMBER, PROJECTBENEFICIARY"
   }
}
```

## Troubleshooting

- Symptom: Submit triggers toast "Failed to fetch config."
   Cause: closeHouseholdRegistration transformer missing or misnamed.
   Fix: Verify transformer config name and deployment.
- Symptom: Success screen never appears.
   Cause: CREATE_EVENT failed before NAVIGATION.
   Fix: Inspect entity repositories and payload mapping for required fields.
- Symptom: Required field error for hidden scanner.
   Cause: Scanner field accidentally set required while hidden.
   Fix: Keep scanner validations empty or set conditional visibility/requirement.
