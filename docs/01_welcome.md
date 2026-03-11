# Prerequisites Setup (Path B Only)

This page is for participants using their own AWS account (Path B). If you're in an AWS-provided Workshop Studio environment (Path A), skip to Module 2: Building the Reservation System.

## Overview

Before starting the workshop, you'll need to:

- Create an Amazon Connect instance
- Enable Amazon Connect Assistant
- Deploy the HotelAPI CloudFormation template
- Verify your setup

Estimated time: 20-25 minutes

## Step 1: Create Amazon Connect Instance

### 1.1 Navigate to Amazon Connect Console

Ensure you're in a supported AWS region for the capabilities you are wanting to build in this workshop. Check Availability of Amazon Connect features by Region for availability of Generative Voice and AI Agents.

1. Open the Amazon Connect console
2. Click Add an instance

### 1.2 Configure Identity Management

- Identity management: Select Store users within Amazon Connect for workshop purposes. If you select another identity type, complete that configuration and return to the workshop.
- Access URL: Enter a unique alias (e.g., myhotel-workshop-[your-initials]-[date])
- This will be your Connect instance URL: https://[alias].my.connect.aws/
- Click Next

### 1.3 Configure Administrator

- Add a new admin: Select Yes
- Username: admin
- Password: A!Ag3nts
- First name: Admin
- Last name: User
- Email: Enter your email address
- Click Next

### 1.4 Configure Telephony

- Incoming calls: ✅ Enable
- Outgoing calls: ✅ Enable
- Click Next

### 1.5 Configure Data Storage

- Accept default settings
- Click Next

### 1.6 Review and Create

- Review your configuration
- Click Create instance
- Wait 2-3 minutes for instance creation to complete

Your Amazon Connect instance is now created! Note your instance access URL for later use.

## Step 2: Enable Amazon Connect Assistant

Amazon Connect Assistant provides AI-powered agent assistance and self-service capabilities.

### Creating your Knowledge Base

1. Navigate to the Amazon S3 console.
2. Create a new bucket, or use an existing bucket

**Step Complete**
Now that you have created your knowledge base in Amazon S3, it's time to move on to creating your Connect Assistant.

### Creating your Connect Assistant

1. Navigate back to the Amazon Connect console
2. Navigate to your Amazon Connect instance by selecting the instance alias from your list of Amazon Connect instances in the AWS Console.
3. In the navigation pane, choose Connect Assistant or Amazon Q, and then choose Add domain.
4. On the Add domain page, choose Create a new domain. In the Domain name box, enter a friendly name that's meaningful to you, such as your organization name, for example, AnyCompany.
5. Under Encryption, select the input box below AWS KMS Key and create a new key. Keep the defaults and provide a name to create the key.

> **Note:** When you enable the Amazon Connect Assistant, by default the domain and connection are encrypted with an AWS owned key. However, you have the option to create or provide two AWS KMS keys:
>
> - One for the Connect Assistant domain, used to encrypt the excerpt provided in the recommendations.
> - Another to encrypt the content imported from Amazon S3, Microsoft SharePoint Online, Salesforce, ServiceNow, ZenDesk, etc. Note that the Connect Assistant search indices are always encrypted at rest using an AWS owned key.

6. On the General Configuration KMS page, edit the key policy.

> **Important:** To use the Connect Assistant with Chat, modify the key policy to allow the kms:Decrypt, kms:GenerateDataKey\*, and kms:DescribeKey permissions to the connect.amazonaws.com service principal. Ensure your AWS Principal is updated by replacing your_accountId with your AWS Account ID number.

```json
{
  "Id": "key-consolepolicy-3",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::your_accountId:root"
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "connect.amazonaws.com"
      },
      "Action": ["kms:Decrypt", "kms:GenerateDataKey*", "kms:DescribeKey"],
      "Resource": "*"
    }
  ]
}
```

7. After confirming the details of your policy, select Save Changes.
8. Return to the tab in your browser for the Amazon Connect Console -> Connect Assistant page, select your KMS key, and select Add domain.

**Step Complete**
Now that you have created your Connect assistant, it's time to move on to adding an integration with your knowledge base to import content.

### Creating your Knowledge Base Integration

1. On the Connect Assistant page, under your domain you just created choose Add integration.
2. On the Add integration page, choose Create a new integration, and then select S3 as the source.
3. In the Integration name box, assign a friendly name to the integration, one that is meaningful to you.
4. Under Connection with S3, select Browse S3 and choose your S3 bucket you configured previously.
5. Under Encryption, select the AWS KMS Key you would like to use to encrypt the files.
6. Select Next.
7. Review your config, and select Add integration.

Amazon Connect Assistant is now enabled! You're ready to create AI agents.

## Step 3: Deploy CloudFormation Template

You'll deploy a CloudFormation template that creates the hotel reservation API backend.

### 3.1 Download Required Files

Download the following files to your computer:

> You may need to right-click and save link as to download depending on your browser

- Download Hotel API CloudFormation Template
- Download Hotel Data
- Download API Specification

### 3.2 Create S3 Bucket and Upload Files

1. Open the Amazon S3 console
2. Click Create bucket
3. Bucket name: Enter a unique name like hotel-workshop-[your-initials]-[date]
   - Example: hotel-workshop-nw-20260808
4. AWS Region: Select the region you deployed your Amazon Connect instance in
5. Leave all other settings as default
6. Click Create bucket
7. Click on your newly created bucket name
8. Click Upload
9. Click Add files and select the two files you downloaded:
   - hotels.min.json
   - hotel-api-openapi.yaml
10. Click Upload
11. Wait for the upload to complete

> **Important:** Make note of your bucket name - you'll need it in the next step!

### 3.3 Deploy CloudFormation Stack

1. Open the CloudFormation console
2. Ensure you're in the same region your Amazon Connect instance and S3 bucket are in (check the top-right corner)
3. Click Create stack → With new resources (standard)

**Create stack - Step 1: Specify template:**

- Select Upload a template file
- Click Choose file and select the hotel-api-customer.yaml file you downloaded
- Click Next

**Create stack - Step 2: Specify stack details:**

- Stack name: Enter hotel-api-stack
- Parameters:
  - OpenApiSpecUrl: Enter the S3 URI for your OpenAPI spec file
    - Format: s3://[your-bucket-name]/hotel-api-openapi.yaml
    - Example: s3://hotel-workshop-nw-20260808/hotel-api-openapi.yaml
  - SeedDataUrl: Enter the S3 URI for your hotel data file
    - Format: s3://[your-bucket-name]/hotels.min.json
    - Example: s3://hotel-workshop-nw-20260808/hotels.min.json
- Click Next

> **Important:** Use S3 URIs (s3://bucket/key format), not HTTPS URLs. The Lambda functions will use their IAM roles to securely access your private S3 bucket.

**Create stack - Step 3: Configure stack options:**

- Leave all settings as default
- Scroll to the bottom and check the box: I acknowledge that AWS CloudFormation might create IAM resources
- Click Next

**Create stack - Step 4: Review and create:**

- Review your configuration
- Click Submit
- Wait for the stack creation to complete (approximately 5-7 minutes)
- The status will change from CREATE_IN_PROGRESS to CREATE_COMPLETE
- You can click the refresh icon to update the status

### 3.4 Retrieve Stack Outputs

Once the stack shows CREATE_COMPLETE:

1. Click on the Outputs tab
2. You'll see three important values - copy these to a text file for later use:
   - HotelApiUrl: Your API Gateway endpoint URL
   - ApiKey: Your API authentication key
   - OpenApiSpecS3Location: S3 location of your OpenAPI specification

Your hotel reservation API is now deployed and ready to use!

## Cost Estimate

Running this workshop in your own AWS account will incur costs for the following services:

### Core Services

- **Amazon Connect:** ~$0.038 per minute of usage
  - Note: Amazon Bedrock model charges are included in Amazon Connect Unlimited AI pricing, not billed separately
- **Amazon Connect Customer Profiles:** (pricing)
- **API Gateway:** $3.50 per million requests after free tier
- **AWS Lambda:** $0.20 per 1M requests after free tier
- **Amazon DynamoDB:** $0.25 per GB-month for storage
- **Amazon S3:** $0.023 per GB-month for storage
- **Amazon Lex:** $0.00075 per voice request, $0.004 per text request
- **AWS KMS:** $1.00 per month per customer managed key
- **CloudWatch Logs:** $0.50 per GB ingested

### Optional Services (Module 5)

- **AWS Secrets Manager:** $0.40 per secret per month (Only if using 3p TTS integration)

### Estimated Total Cost

- **Short workshop session (2-3 hours):** $2-5
- **Full day with exploration:** $5-10

Most services have free tiers that may reduce costs for new AWS accounts. The primary ongoing cost is the KMS key ($1/month). See individual pricing pages for free tier details.

> **Tip:** Complete the cleanup steps promptly after finishing the workshop to minimize costs, especially the KMS keys and any running Lambda functions.

## Cleanup Instructions

After completing the workshop, clean up resources to avoid ongoing charges:

### Delete CloudFormation Stack

```bash
aws cloudformation delete-stack --stack-name hotel-api-stack --region [Your Region]
```

### Delete S3 Bucket

```bash
aws s3 rb s3://hotel-workshop-[your-initials] --force --region [Your Region]
```

### Delete Connect Instance

> Deleting the Connect instance will permanently remove all configurations, recordings, and data. Ensure you've exported any needed information first.

1. Open the Amazon Connect console
2. Select your instance
3. Click Delete
4. Type the instance name to confirm
5. Click Delete

## What's Next?

With all prerequisites complete, you're ready to start building your AI-powered hotel reservation system!

Continue to: Module 2: Building the Reservation System
