```KQL
// let ThreatIntelFeed = externaldata(http_user_agent: string)[@"https://raw.githubusercontent.com/mthcht/awesome-lists/main/Lists/suspicious_http_user_agents_list.csv"] with (format="csv", ignoreFirstRecord=True);
let user_agent_whitelist = dynamic(["Nessus", "EndNote", "Mozilla"]);
let host_whitelist = dynamic([""]);
let ExcludedNets = datatable(ExcludedNet: string)
 ['']; 
// let SuspiciousUserAgent = materialize (
// ThreatIntelFeed
// | distinct http_user_agent
// );
DeviceNetworkEvents
| where ActionType == "HttpConnectionInspected"
| extend json = todynamic(AdditionalFields)
| extend direction = tostring(json.direction), method = tostring(json.method), user_agent = tostring(json.user_agent), host = tostring(json.host)
| where direction == "Out"
| evaluate ipv4_lookup(ExcludedNets, RemoteIP, ExcludedNet, return_unmatched = true)
| where isempty(ExcludedNet)
| where not(isempty(user_agent))
// | where user_agent in (SuspiciousUserAgent)
| where not (user_agent has_any(user_agent_whitelist))
| where not (host has_any(host_whitelist))
| summarize count() by user_agent
```
