```KQL
let CertReport = (externaldata(CRHash:string, CRMalware: string, CRSigner: string, CRIssuerShort: string, CRIssuer: string, CRSerial: string, CRThumbprint: string, CRValidFrom: datetime, CRValidTo: datetime, CRCountry: string, CRState: string, CRLocality: string, CREmail: string)
[@"https://certcentral.org/api/download_csv"]
with (format=csv))
| extend CRSerial = tolower(CRSerial);
union DeviceProcessEvents, DeviceImageLoadEvents, DeviceFileEvents
| where FolderPath matches regex @"(?i):\\Users\\[^\\]+\\Downloads\\.*(exe|dll|msi|msix)$"
   or FolderPath matches regex @"(?i):\\Users\\[^\\]+\\AppData\\Local\\[^\\]+\\.*(exe|dll|msi|msix)$"
   or FolderPath matches regex @"(?i):\\Users\\[^\\]+\\AppData\\Roaming\\[^\\]+\\.*(exe|dll|msi|msix)$"
| where FolderPath !has "\\AppData\\Local\\Temp\\"
| distinct DeviceName, SHA1, FolderPath
| join DeviceFileCertificateInfo on DeviceName, SHA1
| join CertReport on $left.Signer == $right.CRSigner
| extend CertSerialMatchesCertReport = iif( CertificateSerialNumber == CRSerial, "TRUE", "FALSE")
| where CertSerialMatchesCertReport == "TRUE"
// whitelist
| project-reorder DeviceName, SHA1, FolderPath, CertSerialMatchesCertReport, CRSerial, CertificateSerialNumber, CRSigner, Signer, SignerHash, CRThumbprint
```
