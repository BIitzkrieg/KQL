```KQL
DeviceNetworkEvents
| where RemoteUrl has_any( @"drive.googleapis.com", @"oauth2.googleapis.com", "sheets.googleapis.com", "www.googleapis.com")
| where InitiatingProcessFileName !in ("chrome.exe", "firefox.exe", "msedge.exe", "edge.exe", "googledrivefs.exe", 'opera.exe')
| invoke FileProfile(InitiatingProcessSHA1)
| where GlobalPrevalence < 100 
| where InitiatingProcessFolderPath contains @"\appdata\"
| join kind=inner (
    DeviceInfo
    | project DeviceId, OSPlatform
) on DeviceId
| where OSPlatform contains "windows"
```
