# P2 – Build your first AI agent (Automated daily weather agent)

## Workflow Overview
**Purpose:** Answer natural language questions about today’s weather using live data and reasoning.

This workflow is an **AI Agent**, not a simple automation.  
It can understand questions, decide what data is needed, and respond with judgement.

**Trigger:** User asks a question in chat (`When chat message received`)

---

## What makes this an Agent (not automation)
The earlier weather workflow followed fixed steps.  
This agent can answer questions like:
- Should I carry an umbrella today?
- Is it a good day for an evening walk?
- What clothes should I wear today?

These require reasoning, not just data lookup.

---

## Workflow Steps
1. User asks a question in chat
2. AI understands the intent behind the question
3. AI decides to use live weather data
4. AI reasons and responds in natural language

---

## Prerequisites

### 1. OpenWeatherMap API Key
Used to fetch live weather data.

**Steps:**
1. Go to https://home.openweathermap.org/users/sign_up
2. Sign up or log in
3. Go to **API Keys**
4. Create a new API key
5. Copy and save the key (you will use it in n8n)

---

### 2. Google Gemini API Key
Used by the AI agent for understanding and reasoning.

#### Step 1: Get Gemini API Key
1. Go to https://aistudio.google.com/
2. Sign in with your Google account
3. Click your profile → **API Keys**
4. Click **Create API Key**
5. Copy and save the key securely

#### Step 2: Add Gemini API Key to n8n
1. In n8n, go to **Credentials**
2. Click **Create New**
3. Search for **Google Gemini (PaLM) API**
4. Paste the API key
5. Save the credentials

---

### 3. Chat Interface
A chat trigger to receive user questions.  
This can be:
- n8n built-in chat UI
- Slack
- Telegram

---

### 4. Test Data
Example user questions:
- Should I carry an umbrella today?
- What’s the humidity right now?
- Is it a good day for an evening walk?

Example ZIP codes:
- 400051, IN (Mumbai – Jio World Centre)
- 122001, IN (Gurugram)

---

## n8n Nodes Used

### Node 1: When chat message received
**Type:** `When chat message received`

**Purpose:** Triggers the workflow when a user asks a question.

| Parameter | Value |
|---------|------|
| **User Message** | `{{ $json.chatInput }}` |

---

### Node 2: Weather Agent
**Type:** `AI Agent`

**Purpose:** Central agent that understands the question, uses tools, and reasons before answering.

**What this node does:**
- Interprets the user’s question
- Decides whether weather data is required
- Calls the weather tool if needed
- Generates a human-like answer

---

### Node 3: Language model
**Type:** `Google Gemini Chat Model`

**Purpose:** Provides language understanding, reasoning, and response generation.

| Parameter | Value |
|---------|------|
| **Credentials** | Google Gemini API |
| **Model** | Default / Recommended |

---

### Node 4: Memory
**Type:** `Simple Memory`

**Purpose:** Remembers recent conversation context during the session so the agent can respond coherently.

---

### Node 5: Weather tool
**Type:** `OpenWeatherMap Tool (Current Weather)`

**Purpose:** Fetches live weather data used by the agent to reason.

#### Weather tool configuration
| Parameter | Value |
|--------|------|
| **Credential** | OpenWeatherMap API |
| **Operation** | Current Weather |
| **Format** | Metric |
| **Location Type** | ZIP Code |
| **ZIP Code** | 400051, IN (Jio World Centre, Mumbai) |
| **Language** | en |

---

## Output
A conversational response that:
- Understands the user’s question
- Uses live weather data
- Applies reasoning
- Responds in clear, natural language

Example response:
> “It looks warm and humid today, with a chance of light rain. Carrying an umbrella would be a good idea, especially if you’re heading out in the evening.”

