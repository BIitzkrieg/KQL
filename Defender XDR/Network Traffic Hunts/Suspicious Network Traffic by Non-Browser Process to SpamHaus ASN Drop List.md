``` KQL
let CIDRASN = externaldata (CIDR: string, CIDRASN:int, CIDRASNName:string, CIDRSource:string)
[@"https://firewalliplists.gypthecat.com/lists/kusto/kusto-cidr-asn.csv.zip"]
with(ignoreFirstRecord=true);
let ASNDropRaw= externaldata ( asn:int, rir:string, domain:string,cc:string, asname:string)
[@"https://www.spamhaus.org/drop/asndrop.json"]
with (format="multijson");
let ASNDropList= ASNDropRaw
| where isnotempty( asn)
| distinct asn;
let CIDRSOfInterest= CIDRASN
| where CIDRASN in (ASNDropList)
| project CIDR, ASN=CIDRASN;
DeviceNetworkEvents
| where ActionType == "ConnectionSuccess"
| where isnotempty(RemoteIP)
| where InitiatingProcessFileName !in ('chrome.exe', 'firefox.exe', 'edge.exe', 'msedge.exe', 'opera.exe', 'brave.exe')
| evaluate ipv4_lookup(CIDRSOfInterest,RemoteIP,CIDR,return_unmatched=false) 
| extend IsMaliciousASN=true   
| lookup ASNDropRaw on $left.ASN == $right.asn  
| project Timestamp, DeviceName, InitiatingProcessFileName, InitiatingProcessId, RemoteIP, ASN, CIDR, asname, domain, cc, rir, IsMaliciousASN
| join kind=leftouter (
    DeviceProcessEvents
    | project 
        DeviceName,
        InitiatingProcessFileName = FileName,
        InitiatingProcessId,
        InitiatingProcessMD5Hash = InitiatingProcessMD5
) on DeviceName, InitiatingProcessFileName, InitiatingProcessId
| project 
    Timestamp,
    DeviceName,
    InitiatingProcessFileName,
    InitiatingProcessId,
    InitiatingProcessMD5Hash,
    RemoteIP,
    ASN,
    CIDR,
    asname,
    domain,
    cc,
    rir,
    IsMaliciousASN
```
