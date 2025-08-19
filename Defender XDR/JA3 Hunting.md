```KQL
DeviceNetworkEvents
| where ActionType contains "SslConnectionInspected" 
| extend parsed = parse_json(AdditionalFields)
| where parsed.ja3 in (externaldata(Ja3Hash: string) [@"https://raw.githubusercontent.com/0xAnalyst/DefenderATPQueries/main/Malja3fingerpints"])

```
