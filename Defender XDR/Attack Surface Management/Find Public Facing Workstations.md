```KQL
let ExcludedNets = datatable(ExcludedNet: string)
 [''];
DeviceInfo
| where IsInternetFacing == "true"
| join kind=inner (DeviceNetworkEvents) on DeviceId
| evaluate ipv4_lookup(ExcludedNets, PublicIP, ExcludedNet, return_unmatched = true)
| where isempty(ExcludedNet)
| where OSPlatform !contains "server"
| project DeviceName, DeviceId, PublicIP, OSArchitecture, OSPlatform
| summarize by DeviceName, DeviceId, PublicIP, OSArchitecture, OSPlatform

```
