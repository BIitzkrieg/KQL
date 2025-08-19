```KQL
search in (CloudAppEvents) ActionType: "mailitemsaccessed"
| where Timestamp > ago(90d)
| where ipv4_is_private(IPAddress) == false
| summarize  day_seen=dcount(bin(Timestamp, 1d)), arg_max(Timestamp, *) by tostring(IPTags)
| where IPTags has_any("malware", "anonymous", "ocean", "spray", "attack")
| project Timestamp, ActionType, Application, AccountDisplayName, IPAddress, City, ISP, UserAgent, IPTags, IPCategory, RawEventData
```
