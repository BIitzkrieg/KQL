```KQL
let cidr_ranges = datatable(CIDRRange: string)
['91.215.85.0/24', '91.202.233.0/24'];
DeviceNetworkEvents
| evaluate ipv4_lookup(cidr_ranges, RemoteIP, CIDRRange, return_unmatched = false)
//| where isempty(CIDRRange)
| where ActionType in ("ConnectionSuccess")
| project Timestamp, DeviceId, DeviceName, InitiatingProcessFileName=tolower(InitiatingProcessFileName), InitiatingProcessId, RemoteIP, RemoteUrl, RemotePort, NetworkTimestamp = Timestamp

```
