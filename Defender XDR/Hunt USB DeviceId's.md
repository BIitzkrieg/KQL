```KQL
DeviceEvents
| where ActionType contains "pnp" or ActionType contains "usb"
| extend json = todynamic(AdditionalFields)
| extend DeviceId = tostring(json.DeviceId)
| where DeviceId contains @"USB\VID_1D6B&PID_0044\"
| project Timestamp, DeviceName, ActionType, DeviceId, AdditionalFields
```
