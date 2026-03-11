fields Identifier, ContactFlowName, ContactFlowModuleType, Parameters.Key, Parameters.Value, Parameters.SecondValue, Results, @timestamp, @message
| filter ContactId = "31f4cfdc-eec9-43cc-8807-22b8b3d9376b"
| sort @timestamp asc

fields @timestamp, @message
| filter @message like "31f4cfdc-eec9-43cc-8807-22b8b3d9376b"
| sort @timestamp desc
| limit 1000
