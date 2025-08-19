```KQL
let LOLDrivers = externaldata (Category:string, KnownVulnerableSamples:dynamic, Verified:string ) [h@"https://www.loldrivers.io/api/drivers.json"]
     with (
       format=multijson,
       ingestionMapping=@'[ {"Column":"Category","Properties":{"Path":"$.Category"}}, {"Column":"KnownVulnerableSamples","Properties":{"Path":"$.KnownVulnerableSamples"}}, {"Column":"Verified","Properties":{"Path":"$.Verified"}}]'
     )
| mv-expand KnownVulnerableSamples
| extend SHA1 = tostring(KnownVulnerableSamples.SHA1), SHA256 = tostring(KnownVulnerableSamples.SHA256);
// you can filter the drivers further based on category or verified status
DeviceEvents
| where ActionType == "DriverLoad"
| join kind=inner (LOLDrivers | where isnotempty(SHA256) | project SHA256, Category, Verified) on SHA256
| union (
DeviceEvents
| where ActionType == "DriverLoad"
| join kind=inner (LOLDrivers | where isnotempty(SHA1) | project SHA1, Category, Verified) on SHA1
)
| summarize dcount(DeviceName) by InitiatingProcessFileName, FileName, FolderPath, SHA256, SHA1, Category, Verified
//LOLDrivers
//| where isnotempty(SHA256)
//| project SHA256
```
