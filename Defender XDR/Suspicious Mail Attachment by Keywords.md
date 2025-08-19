```KQL
EmailAttachmentInfo
| where FileName has_any ("invoice", "order", "ups", "fedex")
| where FileName matches regex @".*\.zip$|.*\.rar$|.*\.iso$"
| join EmailEvents on NetworkMessageId
| where not(SenderFromAddress has_any (""))
| where DeliveryLocation !contains "Dropped"
| project Timestamp, SenderFromAddress, SenderDisplayName, RecipientEmailAddress, Subject, FileName, DeliveryLocation, NetworkMessageId, ReportId
| sort by Timestamp
```
