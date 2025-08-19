```KQL
let DeviceInformation = DeviceInfo
    | project DeviceId, DeviceName, Timestamp, ReportId;
let BrowserExtensions = materialize (
    (externaldata(report:string)
    [@"https://raw.githubusercontent.com/mthcht/awesome-lists/main/Lists/Browser%20Extensions/browser_extensions_list.csv"]
    with (format = "txt"))
    | extend report = parse_csv(report)
    | extend ExtensionName = tostring(report[0])
    | extend ExtensionId = tostring(report[1])
    | extend MetadataCategory = tostring(report[2])
    | extend MetadataType = tostring(report[3])
    | extend MetadataLink = tostring(report[4])
    | extend MetadataComment = tostring(report[5])
    | project ExtensionName, ExtensionId, MetadataCategory, MetadataType, MetadataLink, MetadataComment
    );
union (
    BrowserExtensions
    | join (
        DeviceTvmBrowserExtensions
        ) on ExtensionName
        | join ( 
        DeviceInformation
        ) on DeviceId
    | project Timestamp, ExtensionId, MetadataCategory, MetadataType, MetadataLink, MetadataComment, ReportId, DeviceId
    )
```
