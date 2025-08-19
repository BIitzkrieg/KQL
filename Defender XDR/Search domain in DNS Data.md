```KQL
DeviceNetworkEvents
| where ActionType == "DnsConnectionInspected"
| extend AdditionalFields=parse_json(AdditionalFields)
| extend Query=tostring(AdditionalFields.query)
// Update with domain you want to search - note this is an expensive search to run
| where Query == 'wqmjufpmvn.xyz'
```
