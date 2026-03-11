# Handling Customer Questions

Handling Customer Questions with Knowledge Base
In this module, you'll enable your AI agent to answer customer questions about hotel policies, amenities, and services using a knowledge base.

What You'll Build
Your AI agent currently handles reservations through the hotel API. Now you'll add the ability to answer questions like:

"What's your cancellation policy?"
"Does the Seattle hotel have a pool?"
"What time is check-in?"
"Do you allow pets?"
How It Works
The knowledge base integration uses the Amazon Connect Assistant to:

Store FAQ documents in an S3 bucket
Index the content for semantic search
Retrieve relevant information when customers ask questions
Provide accurate answers based on the FAQ content
Module Tasks
Task Description
1 Upload FAQ documents to the knowledge base bucket
2 Add the Retrieve tool to your AI agent
3 Test knowledge base queries
Prerequisites
Before starting this module, ensure you have:

Completed Module 2 (Reservation System)
Your AI agent is working and can make reservations
Access to the AWS Console
Let's get started!

# Task 1: Upload FAQ Documents

The knowledge base needs FAQ documents to answer customer questions. In this task, you'll upload hotel FAQ files to the S3 bucket that was created by CloudFormation for Workshop accounts.

Customer Accounts
For customer accounts, you should have already created an S3 bucket and associated it to your Amazon Connect Assistant in the pre-requisites setup . If you didn't go back and do that now. You will use this bucket to upload the files within this module.
About the FAQ Documents
We've prepared FAQ documents for each hotel property covering:

Amenities - Pool, gym, spa, restaurant, WiFi, parking
Policies - Cancellation, check-in/check-out times, pets, smoking
Services - Room service, concierge, laundry, shuttle
Accessibility - Wheelchair access, accessible rooms, service animals
Each hotel has its own FAQ file with property-specific information.

Step 1: Download the FAQ Documents
Download the hotel FAQ zip file:
Basic setting configurations (module)

Extract the zip file to a folder on your computer

You should see files named like:

hotel-seattle-001.txt
hotel-seattle-002.txt
hotel-newyork-001.txt
etc.
Step 2: Find the Knowledge Base Bucket
Customer Accounts
For customer accounts, use the bucket you created in pre-requisites
Open the workshop-values.txt file you created earlier

Find the KnowledgeBaseBucketName value

Open the S3 console

Find and click on the bucket with that name

Step 3: Upload the FAQ Files
Click Upload

Click Add files and select all the .txt files you extracted

Tip: You can select multiple files by holding Ctrl (Windows) or Cmd (Mac) while clicking

Click Upload

Wait for all files to upload successfully

You should see approximately 300 FAQ files in the bucket when complete.

What's in the FAQ Files?
Here's an example of what a hotel FAQ file contains:

Hotel: The Emerald Cascade - Seattle
Location: Seattle, Washington

AMENITIES

- Rooftop infinity pool with panoramic city views (6am-10pm)
- 24-hour fitness center with Peloton bikes
- Full-service spa offering Pacific Northwest-inspired treatments
- Farm-to-table restaurant featuring local ingredients
- Complimentary high-speed WiFi throughout the property
- Valet parking available ($45/night)

CHECK-IN / CHECK-OUT

- Check-in time: 3:00 PM
- Check-out time: 11:00 AM
- Early check-in available upon request (subject to availability)
- Late check-out available for a fee

CANCELLATION POLICY

- Free cancellation up to 24 hours before check-in
- Cancellations within 24 hours subject to one night's charge

PET POLICY

- Pet-friendly rooms available
- $50 per pet, per night fee
- Maximum 2 pets per room
- Weight limit: 50 lbs per pet

ACCESSIBILITY

- ADA-compliant rooms available
- Wheelchair accessible throughout property
- Service animals welcome at no additional charge
  What's Next?
  The knowledge base will automatically index the new documents. In the next task, you'll add the Retrieve tool to your AI agent so it can search the FAQ content.

# Task 2: Add the Retrieve Tool

Now that your knowledge base is synced, you need to add the Retrieve tool to your AI agent. This tool allows the agent to search the knowledge base and find relevant information to answer customer questions.

Step 1: Navigate to Your AI Agent
In the Amazon Connect admin console, click AI agent designer in the left navigation

Click AI agents

Click on your hotel-booking-agent

Click Edit in Agent Builder

Step 2: Add the Retrieve Tool
In the Tools section, click the Add tool button

For Namespace, select Amazon Connect

For Tool, select Retrieve

For Assistant Association, select your assistant association from the dropdown

Configure the tool settings:

Tool name: RetrieveHotelInfo

Instructions:

1
Search the knowledge base using semantic search to find relevant information about a specific hotel (e.g. amenities, policies, accessibility, etc.). Use the results of the RETRIEVE tool to provide an informed answer to the customer's query. You may use this tool multiple times if you don't have the information.

Retrieve tool instructions

Step 3: Add Examples
Examples help the AI agent understand how to use the tool effectively. Add these examples:

Example 1 - Good response with results:

1
2
3
4
Good example:
<message>
I found your warranty covers parts replacement for any manufacturing defects during the first year. We offer extended warranty coverage for eligible products beyond the manufacturer's warranty period.
</message>

Example 2 - No results found:

1
2
3
4
Example for no results:
<message>
I don't have specific information about that topic available.
</message>

Example 3 - Good query:

1
Good example query: "What is the pet policy for the Emerald Cascade hotel?"

Example 4 - Bad query:

1
Bad example query: "Emerald Cascade"

Step 4: Save and Publish
Click Add to add the tool

Click Publish to update your agent with the new tool

How the Retrieve Tool Works
When a customer asks a question about hotel policies or amenities:

Agent recognizes the question - Identifies it's asking about hotel information rather than making a reservation

Agent calls RetrieveHotelInfo - Searches the knowledge base with a semantic query

Knowledge base returns results - Finds the most relevant FAQ content

Agent synthesizes response - Combines the retrieved information into a natural conversational response

What's Next?
Your AI agent can now answer questions using the knowledge base. In the next task, you'll test it by asking questions about hotel amenities and policies.

# Task 3: Test Knowledge Base Queries

Your AI agent now has access to the knowledge base. Let's test it by asking questions about hotel amenities, policies, and services.

Test Your Agent
Call the phone number you claimed earlier and try asking questions about hotels.

Policy Questions
Try asking about hotel policies:

"What's your cancellation policy?"

"What time is check-in?"

"Can I get a late checkout?"

The agent should retrieve the relevant policy information from the knowledge base and provide a clear answer.

Amenity Questions
Ask about specific hotel amenities:

"Does the Seattle hotel have a pool?"

"Is there a gym at the New York property?"

"Do you have free WiFi?"

"Is parking available?"

Pet Policy
Many travelers ask about pets:

"Do you allow pets?"

"What's the pet fee?"

"Are there weight limits for pets?"

Accessibility
Test accessibility-related questions:

"Do you have wheelchair accessible rooms?"

"Are service animals allowed?"

Combined Queries
Try combining knowledge base questions with reservations:

"I'd like to book a pet-friendly room in Seattle. What's the pet policy there?"

"Do you have any hotels with pools in Las Vegas? I'd like to make a reservation."

What to Expect
The AI agent should:

Recognize the question type - Understand you're asking about hotel information, not making a reservation

Search the knowledge base - Use the Retrieve tool to find relevant FAQ content

Provide accurate answers - Give you information based on the FAQ documents

Offer to help further - Ask if you'd like to make a reservation or have other questions

Troubleshooting
Agent says it doesn't have information:

Verify the knowledge base sync completed successfully
Check that the Retrieve tool is published
Try rephrasing your question
Agent gives generic answers:

Be specific about which hotel or city you're asking about
The knowledge base has property-specific information
Agent tries to make a reservation instead:

Start your question with "I have a question about..." or "Can you tell me about..."
Be clear you're asking for information, not booking
Congratulations!
Your AI agent can now:

✅ Make hotel reservations through the API
✅ Look up and modify existing reservations
✅ Answer questions about hotel policies and amenities
✅ Provide property-specific information from the knowledge base
In the next module, you'll learn how to handle situations where the AI agent needs to escalate to a human agent.

#
