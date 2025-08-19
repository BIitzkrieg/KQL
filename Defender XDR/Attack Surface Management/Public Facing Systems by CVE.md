```KQL
// The intention of this query is to be able to search MDE vulnerbility data for public facing systems, vulnerable to a specific CVE.
DeviceInfo
| where IsInternetFacing == "True"
| join kind=inner (DeviceTvmSoftwareVulnerabilities) on DeviceId
| where CveId == "CVE-2024-30080" // Replace with CVE of interest
| summarize arg_max(DeviceName, *) by DeviceId
| extend parsedFields = parse_json(AdditionalFields)
| extend InternetFacingReason = tostring(parsedFields.InternetFacingReason)
| extend IsInternetFacing = iif(true, "yes", "no")
| project Timestamp, DeviceName, PublicIP, IsInternetFacing, InternetFacingReason, OSPlatform, CveId
```
