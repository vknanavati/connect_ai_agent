# Experiment 1: Change Your Agent Personality

Your AI agent's personality is defined entirely by its prompt. By modifying the system prompt, you can transform Sunny the friendly concierge into... well, anything you want.

## Try These Personalities

Here are some fun personality modifications to try. Update the system prompt in your AI agent configuration to experiment.

### Pirate Concierge

```
You are Captain Sunny, the swashbuckling AI concierge for AnyCompany Hotels!

Arr, ye be here to help landlubbers book the finest quarters across our 50 ports of call. Speak like a friendly pirate - use "arr," "matey," "aye," and nautical terms, but keep it professional enough to actually help customers book rooms.

When confirming reservations, say things like "Yer cabin be secured!" instead of boring corporate speak.
```

### British Butler

```
You are Reginald, the distinguished AI butler for AnyCompany Hotels. You speak with impeccable British formality and grace.

Address customers as "Sir" or "Madam," use phrases like "Very good," "Indeed," and "Shall I arrange that for you?" You're unfailingly polite, slightly formal, but genuinely warm. Think Downton Abbey meets hotel concierge.
```

### Surfer Dude

```
You are Sunny, the totally chill AI concierge for AnyCompany Hotels! You're stoked to help guests find the perfect place to crash.

Use casual surfer lingo like "dude," "rad," "gnarly," and "stoked," but stay helpful and clear. When someone books a room, you might say "Sick! You're all set, dude!" Keep the vibes positive and laid-back.
```

### Robot Assistant

```
You are SUNNY-3000, an advanced hospitality robot for AnyCompany Hotels. You speak in a slightly robotic but friendly manner.

Occasionally reference your "processing units" or say things like "Affirmative" instead of "Yes."

Add subtle robot humor like "My circuits indicate this hotel has excellent reviews."

Stay helpful and warm despite the robotic persona.
```

## How to Update Your Prompt

1. In the Amazon Connect admin console, go to Agent applications → AI agents
2. Click on your hotel-booking-agent
3. Click the Prompts tab
4. Click on your Hotel-Booking-Orchestration-Prompt
5. Find the personality section at the top of the prompt (starts with "You are Sunny...")
6. Replace it with one of the personalities above
7. Click Save, then Publish to create a new version of the prompt
8. Go back to the AI agents page and click on your hotel-booking-agent
9. Click Edit in Agent Builder
10. In the Prompts section, click the version dropdown for your orchestration prompt and select the latest version you just published
11. Click Publish to update the agent with the new prompt
12. Call your agent and hear the difference!

## Tips for Custom Personalities

- Keep the core instructions intact (tool usage, security, formatting)
- Only modify the personality/tone sections
- Test with a few calls to make sure the agent still functions correctly
- The agent should still be helpful—personality is flavor, not a replacement for functionality

## Reset to Default

To go back to the original Sunny personality, use:

```
You are Sunny, the AI concierge for AnyCompany Hotels! You're here to make booking a stay as delightful as finding an extra pillow mint. You're bubbly, warm, and always ready with a smile.

Your superpower? Helping guests book the perfect hotel stay at AnyCompany Hotels.
```

# Experiment 2: Custom Voices with Third-Party TTS

Amazon Connect supports custom text-to-speech voices from third-party providers including ElevenLabs and Deepgram. This workshop has pre-configured the necessary AWS infrastructure (Secrets Manager and KMS), but you'll need to provide your own API key from either provider.

Prerequisites
Sign up for a provider account and generate an API key:
ElevenLabs: https://elevenlabs.io/
Deepgram: https://deepgram.com/
Note your chosen voice ID or model from the provider's documentation
Step 1: Update the Secrets Manager Secret
Workshop account vs Customer account
If you are using a workshop provided account, the workshop has already created a Secrets Manager secret with the necessary permissions. You just need to update it with your API key.

If you are running this in your own account, download the following CloudFormation template and run it in your account to create the necessary assets or manually create a new secrets manager entry.

3p TTS Template

Get the Secret ARN
Open the AWS Secrets Manager console
Find and open the secret ending in -3pTTSApiKey
Under Secret value select Edit
Replace the apiToken placeholder with your API Token from either ElevenLabs or Deepgram
Deepgram users
The workshop template creates the secret with an apiTokenRegion field set to "us". This field is used by ElevenLabs to route requests to the correct region. If you are using Deepgram, you can safely remove the apiTokenRegion field — Deepgram does not require it. Your secret value should look like: {"apiToken":"YOUR_API_KEY"}

Select Save
Copy the Secret ARN - you'll need this for the contact flow
Step 2: Configure the Contact Flow
Open the Main Flow in the Amazon Connect contact flow editor
Find the Set voice block and open its properties
Configure based on your provider:
For ElevenLabs:

Voice provider: ElevenLabs
Model: Set manually → eleven_flash_v2_5 (recommended) or eleven_turbo_v2_5
Voice: Set manually → Enter a Voice ID from the table below
Secrets Manager ARN: Set manually → Paste the ARN from Step 1
Language: Set manually → English (US)
For Deepgram:

Voice provider: Deepgram
Model: Set manually → aura-2
Voice: Set manually → Enter a voice name from the table below (e.g. thalia)
Secrets Manager ARN: Set manually → Paste the ARN from Step 1
Language: Set manually → Choose a language supported by the voice (e.g. English)
How Deepgram model names work
Amazon Connect combines the Model, Voice, and Language values into a single Deepgram model name at runtime. For example, setting Model to aura-2, Voice to thalia, and Language to English maps to the Deepgram model aura-2-thalia-en. See the Deepgram TTS documentation for the full list of supported voices and languages.

Save and Publish your flow
Voice Recommendations
ElevenLabs Voices
Voice ID Name Characteristics
cjVigY5qzO86Huf0OWal Eric Smooth tenor, perfect for agentic use cases
SAz9YHcvj6GT2YYXdXww River Relaxed, neutral, conversational
EXAVITQu4vr4xnSDxMaL Sarah Confident, warm, professional
CwhRBWXzGAHq8TQ4Fs17 Roger Easy going, casual conversations
iP95p4xoKVk53GoZ742B Chris Natural, down-to-earth
Deepgram Voices
With the aura-2 model, the following voices are available:

Voice Characteristics
thalia Warm, conversational female
luna Friendly, approachable female
stella Clear, articulate female
athena Professional, authoritative female
orion Professional male voice
Deepgram voice list
For the full list of Deepgram voices and supported languages, see the Deepgram TTS voice list .

Test Your Custom Voice
Call your Connect phone number and listen to your AI agent with its new voice. Try different voices to find one that matches your brand and use case.

# Summary

Workshop Complete
You've built a complete agentic AI solution for AnyCompany Hotels. Let's recap what you accomplished.

What You Built
Module 2: Reservation System

Deployed an MCP server with hotel availability and booking APIs
Created an AI agent that handles reservations through natural conversation
Connected Customer Profiles for personalized greetings
Enabled Nova Sonic for natural, human-like voice interactions
Module 3: Knowledge Base

Uploaded FAQ documents covering hotel policies and amenities
Added the Retrieve tool so your AI can answer customer questions
Tested knowledge base queries for accurate responses
Module 4: Human Escalation

Configured an Escalation tool that captures conversation context
Built flow logic to detect escalations and route to human agents
Enabled Step-by-step Guides so agents see the full conversation summary
The Result
Your AI agent can now:

Check availability and make reservations across 139 cities
Answer questions about hotel policies, amenities, and services
Recognize complex requests and escalate with full context
Greet returning customers by name
Operate 24/7 without human intervention for routine tasks
When escalation is needed, human agents receive a complete summary—customer intent, sentiment, and conversation history—so customers never repeat themselves.

Next Steps
Explore further:

Add more tools to your AI agent for additional capabilities
Customize the escalation logic for different scenarios
Build custom Step-by-step Guides for specific use cases
Implement analytics to track AI performance and escalation patterns
Learn more:

Amazon Connect AI Agents documentation
Model Context Protocol
Amazon Bedrock
Clean Up
To avoid ongoing charges, delete the CloudFormation stack when you're done experimenting:

Open the CloudFormation console
Select your workshop stack
Click Delete
This removes all resources created during the workshop.

Thanks for completing the workshop. You're now equipped to build intelligent, agentic AI solutions on Amazon Connect.
