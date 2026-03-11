S3 bucket name: hotel-workshop-vn-03102026
AI Agent domain:
name: vn-workshop-domain
Created KMS Key:
KMS key:hotel-vn-kms-key
KMS key ID:a1c616d7-3db0-44e4-a105-152bfccb8471
Integration: vn-integration

-Purpose of KMS key: encrypting the domain and encrypting the content imported from S3

On AI Agent domain page added integration: hotel-workshop-vn-integration

Cloudformation

Amazon Bedrock AgentCore: vn-hotel-workshop-mcp
GatewayID: vn-hotel-workshop-mcp-pqjrioju7w

Third party app:
Customer profile domain:

Bot name:

Assistant ID: 3933fb07-8626-4caf-857e-f53bb2a809be

---

=== Workshop Stack Outputs ===

AgentCoreGatewayDiscoveryUrl: https://viminn-sandbox.my.connect.aws/.well-known/openid-configuration

ConnectInstanceArn:arn:aws:connect:us-west-2:308665918648:instance/ccde1e73-2000-4b14-9a86-d00e80ebfe5a

HotelApiKey:WS3xTRLTZ48IalAo9wAmH7D7uxy6mtH91X9XB2ZR
(API key for providing AgentCore Gateway access to your API)

HotelApiOpenApiSpecUrl:s3://vn-workshop-stack-openapibucket-sjrpgdeapg31/openapi.yaml

HotelApiUrl:https://0nglzljf4b.execute-api.us-west-2.amazonaws.com/dev

---

Commands
1.Delete knowledge base:aws wisdom delete-knowledge-base \
 --knowledge-base-id 50f52457-38c7-4fb8-9f3f-c9f5fd6192c5 \
 --region us-west-2

2. Delete app integration data
   aws appintegrations delete-data-integration \
    --data-integration-identifier 8c3b3c9c-4b84-4e4f-ae25-dffaac51df36 \
    --region us-west-2

aws cloudformation delete-stack --stack-name hotel-api-stack-vn --region us-west-2

aws s3 rb s3://hotel-workshop-vn-03092026--force --region us-west-2
aws cloudformation delete-stack --stack-name hotel-api-stack-vn --region us-west-2

aws s3 rb s3://hotel-workshop-vn-03092026 --force --region us-west-2

aws wisdom list-knowledge-bases --region us-west-2

aws wisdom create-knowledge-base \
 --name "hotel-workshop-kb" \
 --knowledge-base-type EXTERNAL \
 --source-configuration appIntegrations="{appIntegrationArn='arn:aws:app-integrations:us-west-2:308665918648:data-integration/bf3b2155-0bbe-43a1-af87-026d7009176a',objectFields=['Id','Title','Body','Uri']}" \
 --region us-west-2

q to exit less terminal pager
**To search within the current `less` view:**

While the `:` prompt is showing, press **`/`** followed by your search term:

```
/hotel-workshop


Issue 3: AppIntegrations Quota Exceeded Every failed integration attempt was silently creating a partial AppIntegrations data integration record, even though the UI showed failure. This ate up the account quota quickly. We fixed it by deleting the 5 duplicate hotel bucket integrations via CLI, keeping only one.
Issue 4: Wisdom Knowledge Base Quota Exceeded The shared First Fire account had too many orphaned knowledge bases from old/deleted Connect instances. We identified two that belonged to deleted instances (ViminDemo and vnlexbottraining) and safely deleted them to free up slots.
Key lesson learned: In a shared AWS account, quota limits can sneak up on you fast, especially when failed UI operations still create backend resources. The CLI (aws appintegrations list-data-integrations and aws wisdom list-knowledge-bases) was essential for seeing what was actually there versus what the UI was showing.
```
