```KQL
let DomainExclusions = dynamic(["",]);
let MaxAge = ago(14d);
EmailAttachmentInfo
| where FileName endswith ".svg"
| where not(SenderFromAddress has_any (DomainExclusions))
| project Timestamp, SenderFromAddress, RecipientEmailAddress, ThreatTypes, ThreatNames, FileName, NetworkMessageId
    | join (
        EmailEvents
        | where Timestamp > MaxAge
    ) on NetworkMessageId
| project Timestamp, SenderFromAddress, RecipientEmailAddress, FileName, ThreatNames, ThreatTypes, LatestDeliveryLocation, LatestDeliveryAction
```
