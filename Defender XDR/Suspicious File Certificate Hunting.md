```KQL
DeviceFileCertificateInfo
| where isnotempty(Signer)
// Looks for chinese unicode in signer name, can hunt for any key words here, etc.
| where Signer matches regex "[^\x00-\x7F]"
| summarize 
    CountOfSignedFiles = count(),
    SHA1OfFile = make_set(SHA1, 100),
    DeviceNames = make_set(DeviceName, 100)
  by Signer
  | where CountOfSignedFiles <= 500
| order by CountOfSignedFiles desc
```
