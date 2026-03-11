# Cleanup

Cleanup Resources
Important: Complete these cleanup steps promptly to avoid ongoing charges, especially for KMS keys ($1/month each) and any running resources.
This cleanup guide is for participants using their own AWS accounts (Path B). Workshop Studio accounts (Path A) are automatically cleaned up by AWS.

Step 1: Delete CloudFormation Stacks
Delete Individual Stacks (Manual Deployment)
If you deployed stacks individually, delete in this order:

Guides-Flows stack (if created for Module 4)
3pTTS-integration stack (if created for Module 5)
Hotel-api stack
This deletes Lambda functions, DynamoDB tables, and API Gateway
Session-data-lambda stack
Logging stack
For each stack:

1
aws cloudformation delete-stack --stack-name <stack-name> --region us-west-2

Or use the CloudFormation console:

Select the stack
Click Delete
Confirm deletion
Wait for DELETE_COMPLETE status before proceeding to the next stack
Step 2: Clean Up S3 Buckets
CloudFormation cannot delete S3 buckets that contain objects. You must empty them first.

2.1 Identify S3 Buckets
Find buckets created by the workshop:

1
aws s3 ls | grep -E "hotel-workshop|knowledge-base|connect"

Common bucket patterns:

hotel-workshop-[your-initials] (if you created one manually for Path B setup)
workshop-stack-knowledgebase-_ (created by knowledge base stack for FAQ documents)
workshop-stack-openapibucket-_ (created by hotel API stack for OpenAPI spec)
aws-connect-\* (created by Connect for recordings/transcripts)
2.2 Empty and Delete Buckets
For each bucket:

1
2
3
4
5

# Empty the bucket

aws s3 rm s3://[bucket-name] --recursive --region us-west-2

# Delete the bucket

aws s3 rb s3://[bucket-name] --region us-west-2

Or use the S3 console:

Open the S3 console
Select the bucket
Click Empty
Confirm by typing "permanently delete"
After emptying, select the bucket again
Click Delete
Confirm by typing the bucket name
Step 3: Delete Secrets Manager Secrets
If you completed Module 5 (3p TTS integration):

3.1 List Secrets
1
aws secretsmanager list-secrets --region us-west-2 | grep -i 3pTTS

3.2 Delete Secret
1
2
3
4
aws secretsmanager delete-secret \
 --secret-id [secret-name] \
 --force-delete-without-recovery \
 --region us-west-2

Or use the Secrets Manager console:

Open the Secrets Manager console
Select the ElevenLabs secret
Click Actions → Delete secret
Choose Disable waiting period for immediate deletion
Confirm deletion
Step 4: Disable Customer Profiles
If you enabled Customer Profiles in Module 2:

Open the Amazon Connect console
Select your Connect instance
In the left navigation, click Customer profiles
Click Disable Customer Profiles
Confirm by typing "disable"
Disabling Customer Profiles will delete all customer profile data. Export any needed data before disabling.
Step 5: Delete Amazon Connect Instance
Only delete the Connect instance if you created it specifically for this workshop. If you used an existing instance, skip this step.
5.1 Remove Phone Number Association
Before deleting the instance, release the phone number:

Open your Connect instance admin interface
Navigate to Channels → Phone numbers
Select the phone number used in the workshop
Click Release
Confirm release
5.2 Delete the Instance
Open the Amazon Connect console
Select your Connect instance
Click Delete
Type the instance name to confirm
Click Delete
Wait time: 5-10 minutes

Deleting the Connect instance automatically deletes: Amazon Connect Assistant domain, all contact flows, all step-by-step guides, and all recordings and transcripts (if stored in Connect-managed S3).
Step 6: Verify Cleanup
6.1 Check CloudFormation Stacks
1
2
3
4
aws cloudformation list-stacks \
 --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE \
 --region us-west-2 \
 --query 'StackSummaries[?contains(StackName, `workshop`) || contains(StackName, `hotel`)].StackName'

Should return an empty list or no workshop-related stacks.

6.2 Check S3 Buckets
1
aws s3 ls | grep -E "hotel|workshop|knowledge"

Should not show any workshop-related buckets.

6.3 Check Connect Instances
1
aws connect list-instances --region us-west-2

Should not show the workshop instance (if you deleted it).

Troubleshooting Cleanup Issues
CloudFormation Stack Deletion Fails
Issue: Stack deletion fails with dependency errors

Solution:

Check the stack events for specific error messages
Manually delete the blocking resource (often S3 buckets or ENIs)
Retry stack deletion
S3 Bucket Won't Delete
Issue: "Bucket not empty" error

Solution:

1
2
3
4
5
6
7

# Force delete all objects including versions

aws s3api delete-objects \
 --bucket [bucket-name] \
 --delete "$(aws s3api list-object-versions \
 --bucket [bucket-name] \
 --query '{Objects: Versions[].{Key:Key,VersionId:VersionId}}' \
 --max-items 1000)"

Wisdom Knowledge Base Won't Delete
Issue: Knowledge base deletion fails

Solution:

Ensure the S3 bucket is empty first
Check that the AppIntegrations DataIntegration is deleted
Manually delete the knowledge base from the Amazon Connect console:
Navigate to your Connect instance
Click Amazon Connect Assistant
Find the knowledge base and delete it
Retry stack deletion
Connect Instance Won't Delete
Issue: "Instance has associated resources" error

Solution:

Ensure all phone numbers are released
Ensure all contact flows are deleted
Wait 5 minutes and retry
Check for any active contacts or queues
Cost Verification
After cleanup, verify no ongoing charges:

Open the AWS Cost Explorer
Set date range to "Last 7 days"
Group by Service
Verify these services show declining or zero usage:
Amazon Connect
AWS Lambda
Amazon DynamoDB
Amazon API Gateway
AWS KMS
Amazon S3
Cleanup complete! You should see costs drop to near zero within 24 hours.
Estimated Cleanup Time
Automated (delete master stack): 15-20 minutes
Manual (delete individual stacks): 25-35 minutes
Verification: 5 minutes
Total: 20-40 minutes depending on method chosen
