```KQL
// What connections have we seen to a specified ASN?
let CIDRASN = (externaldata (CIDR:string, CIDRASN:int, CIDRASNName:string, CIDRSource:string) ['https://firewalliplists.gypthecat.com/lists/kusto/kusto-cidr-asn.csv.zip'] with (ignoreFirstRecord=true));
let CIDRASNOfInterest = CIDRASN
| where CIDRASN == 14061;
DeviceNetworkEvents
// Whitelist processes
| where InitiatingProcessFileName !in ('chrome.exe', 'firefox.exe', 'edge.exe', 'msedge.exe','shift.exe', 'opera.exe', 'brave.exe', 'msedgewebview2.exe', 'chromium.exe', 'outlook.exe', 'wavebrowser.exe', 'creative cloud.exe', 'acrobat.exe', 'sublime_text.exe')
| where RemoteUrl !in ("servedbyadbutler.com", 'boot.net.anydesk.com', 'g.live.com')
// whitelist more trusted locations
| where InitiatingProcessFolderPath !startswith @"C:\Program Files\"
| where InitiatingProcessFolderPath !startswith @"C:\Program Files (x86)\"
| evaluate ipv4_lookup(CIDRASNOfInterest, RemoteIP, CIDR, return_unmatched=false)
| where ActionType == @"ConnectionSuccess"
| summarize count() by InitiatingProcessFolderPath
```
