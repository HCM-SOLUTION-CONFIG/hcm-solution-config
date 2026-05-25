# permission_handler.json

## Purpose

Provides a centralized permission screen for runtime permissions required by the app.

## Entry and Flow Map

- initialPage: permissionHandler
- flows:
  - permissionHandler (TEMPLATE)

## How It Works

1. Screen renders cards for each permission category.
2. Visibility labels and tags use context permission status fields.
3. Grant button triggers REQUEST_PERMISSION with a specific permission key.
4. UI updates based on context.*PermissionGranted and context.*PermissionStatus.

## Permission Types in Config

- notification
- ignoreBatteryOptimizations
- location
- nearbyWifiDevices (conditional visibility)
- bluetoothScan (conditional visibility)
- camera

## Major Actions Used

- REQUEST_PERMISSION
- BACK_NAVIGATION

## Major UI Formats Used

- card, row, column, icon, textTemplate, button, tag

## How To Use/Modify

- Add a new permission: duplicate one card block and change permission key.
- Hide permission by platform/condition: update card visible expression.
- Change user labels: update i18n label keys in textTemplate/tag/button.

## Validation Checklist

- permission key matches app-side permission resolver.
- status context fields are available in PermissionWrapper context.
- optional cards use safe visibility checks to avoid null errors.

## Example Payload Snippets

```json
{
  "actionType": "REQUEST_PERMISSION",
  "properties": {
    "permission": "location"
  }
}
```

```json
{
  "actionType": "REQUEST_PERMISSION",
  "properties": {
    "permission": "ignoreBatteryOptimizations"
  }
}
```

## Troubleshooting

- Symptom: Grant button visible but permission status never changes.
  Cause: Context status fields are not refreshed after REQUEST_PERMISSION.
  Fix: Verify app-side permission service updates PermissionWrapper context.
- Symptom: Nearby WiFi/Bluetooth cards always hidden.
  Cause: showNearbyWifiDevices/showBluetoothScan context flags are false.
  Fix: Validate platform/version gating logic that sets these flags.
- Symptom: Permission key appears valid but action does nothing.
  Cause: key not recognized by platform permission bridge.
  Fix: Ensure exact key name matches runtime permission mapping.
