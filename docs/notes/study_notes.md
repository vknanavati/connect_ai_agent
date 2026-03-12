PART 1:

Uploaded 2 files to S3 -> yaml with api calls and json with hotel seed info.
SeedData (hotels.min.json)
OpenApiSpec (hotel-api-openapi.yaml)
pure OpenAPI 3.0 specification.
It's documentation:
describes the API's endpoints, request/response schemas, authentication, and examples for consumers (like Bedrock agents, Swagger UI, or developers). Notice the placeholder at the top:
A Bedrock agent, on the other hand, reads the OpenAPI spec the same way a developer would — it relies on the summary, description, and example fields to understand when to call each endpoint and what to pass in. Without those, the agent would have no semantic understanding of what searchHotels actually does or that city should be something like "Seattle".
human/agent-readable API contract.
Deployed cloud formation stack-
hotel-api-customer.yaml
It deploys the entire backend infrastructure: DynamoDB tables, Lambda functions (with all their inline code), API Gateway, IAM roles, and several custom resources. It also contains its own inline, stripped-down OpenAPI spec inside the HotelApi resource (with x-amazon-apigateway-integration extensions) that API Gateway actually uses to configure routing.
CloudFormation Outputs:
${API_GATEWAY_URL}), so the Bedrock agent knows where to actually send requests.
ApiKey → the Bedrock agent needs this to authenticate its calls to API Gateway. Every POST request has X-API-Key auth — without this the agent gets 401s.
OpenApiSpecS3Location → you point your Bedrock agent's Action Group at this S3 location. This is how the agent learns what actions it can take — it reads that spec to understand all the POST operations, their descriptions, and parameters.
Yes. The flow is:
Bedrock Agent → API Gateway → Lambda → DynamoDB
Each POST endpoint maps to its own Lambda function via x-amazon-apigateway-integration in the CloudFormation inline spec:

POST /hotels/search → SearchHotelsFunction (Node.js, queries DynamoDB HotelsTable)
POST /reservations → CreateReservationFunction (Node.js, writes to ReservationsTable)
POST /reservations/customer → GetCustomerReservationsFunction (Python, queries ReservationsTable)
POST /reservations/cancel → CancelReservationFunction (Node.js, updates ReservationsTable)
POST /reservations/modify → ModifyReservationFunction (Python, updates ReservationsTable)

Part 2
Building the Reservation System
You'll connect your AI agent to a hotel reservation API using an MCP (Model Context Protocol) server. The MCP server acts as the bridge between natural language requests and backend systems—translating "I need a room in Seattle" into the right API calls.
The MCP (Model Context Protocol) server acts as an interface layer between the AI Agent and AnyCompany Hotel's backend API. It exposes a set of callable functions that the AI agent can invoke when handling customer requests, including:

searchHotels – find hotels in a specific city
getCustomerReservations – look up a customer's existing bookings
createBooking – make new reservations
modifyBooking – update existing ones
cancelBooking – cancel reservations
In plain terms, it translates natural language requests (like "I need a suite in Seattle for next weekend") into precise, structured API calls against the hotel's backend system.
Yes — the MCP server is hosted within Amazon Bedrock AgentCore Gateway. Specifically:
AgentCore Gateway is the MCP server in this architecture. You're not building a standalone server from scratch; you're configuring AgentCore Gateway to wrap your existing Hotel REST API and expose it as an MCP-compatible interface.
The Gateway handles the inbound authentication (JWT from Amazon Connect) and outbound authentication (API key to the Hotel API), sitting securely in between.
The OpenAPI schema you provide tells AgentCore Gateway which endpoints exist and how to call them, so it can surface them as tools to the AI agent.
So in short: AgentCore Gateway is the MCP server — it's Amazon's managed infrastructure for hosting and securing these agent-to-API connections.

Task 2: Create the MCP server in AgentCore Gateway
In AgentCore:
Add API Key to Identity
Create Gateway
Inbound auth - This controls how Amazon Connect authenticates when calling your gateway.
Target-endpoints that define one or more tools as part of this gateway

---

CloudFormation

5 API endpoints

API Gateway:
API Gateway acts as the "front door" and handles:
Security

Validates the API key (X-API-Key) before the request even reaches Lambda
Rejects unauthorized requests immediately without wasting Lambda compute

Protocol Translation

Lambda natively speaks AWS events, not HTTP
API Gateway converts incoming HTTP requests (the kind Amazon Connect sends) into the Lambda event format, and converts Lambda's response back into an HTTP response

Traffic Management

Handles rate limiting so Lambda doesn't get overwhelmed
Can throttle requests if too many come in at once

SeedData (hotels.min.json) This is the actual hotel data that gets loaded into DynamoDB — things like hotel names, locations, room types, availability, and pricing. "Seeding" means pre-populating the database with initial data so the hotel reservation system has something to work with when you start testing it.
OpenApiSpec (hotel-api-openapi.yaml) This is the API specification file that describes your hotel reservation API — things like what endpoints exist, what parameters they accept, and what they return. For example it defines endpoints like "search for available rooms" or "make a reservation." Amazon Connect uses this spec to understand how to call your API when the AI agent needs to look up hotel information.
Think of it this way:
The OpenAPI spec is the blueprint describing how the API works
The seed data is the content that the API serves up when called
They're two completely different things, which is why swapping them causes the Lambda function to fail — it was trying to parse hotel room descriptions as a JSON database, but got API endpoint definitions instead.
