```KQL
let BrowserExtMalwareProtectionKB = DeviceTvmBrowserExtensionsKB 
    | project ExtensionId, ExtensionName, ExtensionRisk, PermissionName;
let BrowserExtMalwareProtection = DeviceTvmBrowserExtensions
    | project ExtensionId, DeviceId;
let DeviceInformation = DeviceInfo
    | project DeviceId, DeviceName, Timestamp, ReportId;
union BrowserExtMalwareProtection, BrowserExtMalwareProtectionKB,
        DeviceInformation
    | summarize by ExtensionId, DeviceId
    | join ( BrowserExtMalwareProtectionKB ) on ExtensionId
    | join kind=rightouter ( BrowserExtMalwareProtection ) on ExtensionId
    | join ( DeviceInformation ) on DeviceId
    | summarize DeviceCount=dcount(DeviceName), arg_max(Timestamp, *) by ExtensionName, ExtensionRisk
    | sort by DeviceCount asc, ExtensionRisk
```
