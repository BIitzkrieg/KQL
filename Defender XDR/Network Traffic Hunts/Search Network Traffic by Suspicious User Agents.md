```KQL
let ThreatIntelFeed = externaldata(http_user_agent: string)[@"https://raw.githubusercontent.com/mthcht/awesome-lists/main/Lists/suspicious_http_user_agents_list.csv"] with (format="csv", ignoreFirstRecord=True);
let ExcludedNets = datatable(ExcludedNet: string)
 [''];
let SuspiciousUserAgent = materialize (
ThreatIntelFeed
| distinct http_user_agent
);
DeviceNetworkEvents
| evaluate ipv4_lookup(ExcludedNets, RemoteIP, ExcludedNet, return_unmatched = true)
| where isempty(ExcludedNet)
| where ActionType == "HttpConnectionInspected"
| extend json = todynamic(AdditionalFields)
| extend direction = tostring(json.direction), method = tostring(json.method), user_agent = tostring(json.user_agent)
| where direction == "Out"
| where user_agent in (SuspiciousUserAgent)
```
