# P3 – Resume screening assistant (Import and run the workflow)

## Step 1: Import the workflow JSON into n8n

### What you will do in this step
You will import the provided JSON workflow into n8n and run one test submission.

---

## 1. Import the JSON file
1. Open n8n.
2. Go to **Workflows**.
3. Click **Import** (or **Add workflow** → **Import from file**).
4. Select the file: `Resume screening specialist.json`
5. Click **Import**.
6. Rename the workflow to: `P3 – Resume screening assistant (baseline)`

---

## 2. Check the nodes are present
You should see these nodes (names may vary slightly):
1. **Form Trigger** (Resume Screening Form)
2. **Extract from File** (PDF text extraction)
3. **Edit Fields / Set** (creates clean fields)
4. **AI Agent** (Gemini)
5. **Clean output / Set** (removes formatting)
6. **Parse JSON / Code**
7. **Google Sheets** (Append row)

---

## 3. Connect credentials (required to run)
### A) Google Gemini (for the AI Agent)
1. Click the **AI Agent** node.
2. In the model section, ensure it uses **Google Gemini Chat Model**.
3. If credentials are missing:
   - Go to **Credentials** → **Create New**
   - Search: **Google Gemini (PaLM) API**
   - Paste your Gemini API key
   - Save
4. Return to the workflow and select this credential in the model node.

### B) Google Sheets (for logging results)
1. Click the **Google Sheets** node.
2. Select your Google account credential.
3. Confirm the spreadsheet and sheet tab are selected correctly.
   - If it errors, create the credential:
     - **Credentials** → **Create New** → Google Sheets OAuth → Sign in → Allow access

---

## 4. Run a test (one resume)
1. Click **Execute workflow** (or open the **Form Trigger** node and click the form test link if shown).
2. In the form:
   - Upload a sample resume PDF
   - Paste a short job description
3. Submit the form.
4. Confirm:
   - The workflow run completes without errors
   - A new row is added in Google Sheets

---

## Success check
You are done with Step 1 when:
- The workflow imports successfully
- You run 1 form submission end-to-end
- A row appears in Google Sheets
---

---

## n8n Nodes Used (configuration guide)

Below are the **exact node values** from the imported JSON, plus **why each node exists** and **what breaks if you remove it**.

---

### Node 1: Resume Screening Form
**Type:** `Form Trigger (n8n-nodes-base.formTrigger)`

**Purpose:** Collects the resume PDF and job description from the user.

#### Configuration (actual values)
| Field | Value |
|---|---|
| **Form Title** | Resume Screening Form |
| **Form Description** | Upload candidate resume and provide job details for AI-powered screening |
| **Form Fields** | **1) Resume PDF**<br>Field Type: `file`<br>Accept File Types: `pdf`<br>Required: `true`<br><br>**2) Job Description**<br>Field Type: `textarea`<br>Required: `true` |
| **Options** | `{}` |

#### Why this node is used
- It creates a simple input UI for non-technical users to submit a resume + job description.

#### If you remove this node
- There is no way to collect inputs, so the workflow cannot start.

---

### Node 2: Extract from File
**Type:** `Extract from File (n8n-nodes-base.extractFromFile)`

**Purpose:** Extracts text from the uploaded PDF so the AI can read it.

#### Configuration (actual values)
| Field | Value |
|---|---|
| **Operation** | `pdf` |
| **Binary Property Name** | `Resume_PDF` |
| **Options** | `{}` |

#### Why this node is used
- The AI cannot reliably compare a PDF file directly. It needs plain text.

#### If you remove this node
- You will send a file object (or nothing) to the AI, so the AI will either fail or guess.

---

### Node 3: Edit Fields
**Type:** `Set (n8n-nodes-base.set)`

**Purpose:** Creates clean, clearly named fields for the AI.

#### Configuration (actual values)
| Output field | Expression (actual) | What it means |
|---|---|---|
| `resumeText` | `={{ $json.text}}` | Takes extracted resume text from Node 2 |
| `jobDescription` | `={{ $('Resume Screening Form').item.json['Job Description'] }}` | Takes the Job Description text from the form |

| Setting | Value |
|---|---|
| **Options** | `{}` |

#### Why this node is used
- It standardizes inputs into two clean fields: `resumeText` and `jobDescription`.
- It makes the AI prompt simple and consistent.

#### If you remove this node
- The AI node will not find `resumeText` and `jobDescription`.
- Your prompt variables will be blank, leading to bad or empty outputs.

## n8n Nodes Used (configuration guide)

Below are the **exact node values** from the imported JSON, plus **why each node exists** and **what breaks if you remove it**.

---

### Node 1: Resume Screening Form
**Type:** `Form Trigger (n8n-nodes-base.formTrigger)`

**Purpose:** Collects the resume PDF and job description from the user.

#### Configuration (actual values)
| Field | Value |
|---|---|
| **Form Title** | Resume Screening Form |
| **Form Description** | Upload candidate resume and provide job details for AI-powered screening |
| **Form Fields** | **1) Resume PDF**<br>Field Type: `file`<br>Accept File Types: `pdf`<br>Required: `true`<br><br>**2) Job Description**<br>Field Type: `textarea`<br>Required: `true` |
| **Options** | `{}` |

#### Why this node is used
- It creates a simple input UI for non-technical users to submit a resume + job description.

#### If you remove this node
- There is no way to collect inputs, so the workflow cannot start.

---

### Node 2: Extract from File
**Type:** `Extract from File (n8n-nodes-base.extractFromFile)`

**Purpose:** Extracts text from the uploaded PDF so the AI can read it.

#### Configuration (actual values)
| Field | Value |
|---|---|
| **Operation** | `pdf` |
| **Binary Property Name** | `Resume_PDF` |
| **Options** | `{}` |

#### Why this node is used
- The AI cannot reliably compare a PDF file directly. It needs plain text.

#### If you remove this node
- You will send a file object (or nothing) to the AI, so the AI will either fail or guess.

---

### Node 3: Edit Fields
**Type:** `Set (n8n-nodes-base.set)`

**Purpose:** Creates clean, clearly named fields for the AI.

#### Configuration (actual values)
| Output field | Expression (actual) | What it means |
|---|---|---|
| `resumeText` | `={{ $json.text}}` | Takes extracted resume text from Node 2 |
| `jobDescription` | `={{ $('Resume Screening Form').item.json['Job Description'] }}` | Takes the Job Description text from the form |

| Setting | Value |
|---|---|
| **Options** | `{}` |

#### Why this node is used
- It standardizes inputs into two clean fields: `resumeText` and `jobDescription`.
- It makes the AI prompt simple and consistent.

#### If you remove this node
- The AI node will not find `resumeText` and `jobDescription`.
- Your prompt variables will be blank, leading to bad or empty outputs.

---

### Node 4: AI Agent
**Type:** `AI Agent (@n8n/n8n-nodes-langchain.agent)`

**Purpose:** Compares resume vs job description and returns a structured JSON assessment.

#### Configuration (actual values)
| Field | Value |
|---|---|
| **Prompt Type** | `define` |
| **Text (prompt)** | (exact text below) |
| **Options** | `{}` |

#### Prompt text (actual)
text
=Analyze the following resume against the job description and provide a structured assessment.

RESUME:
{{ $json.resumeText }}

JOB DESCRIPTION:
{{ $json.jobDescription }}

Please analyze and return ONLY a valid JSON object with these exact fields:
{
  "candidate_name": "[extract candidate's full name from resume]",
  "match_score": [number from 0-100],
  "key_skills_match": [list of matching skills],
  "experience_match": "[brief assessment]",
  "strengths": [list of key strengths],
  "gaps": [list of areas where candidate may fall short],
  "recommendation": "[hire/interview/reject with brief reason]"
}

IMPORTANT: Return ONLY the raw JSON object with no markdown formatting, no code blocks, no backticks, no extra text - just the pure JSON starting with { and ending with }.

---

---

### Node 5: Google Gemini Chat Model
**Type:** `Google Gemini Chat Model (@n8n/n8n-nodes-langchain.lmChatGoogleGemini)`

**Purpose:** Provides the reasoning and language capability used by the AI Agent.

#### Configuration (actual values in JSON)
| Field | Value |
|---|---|
| **Options** | `{}` |

#### What you must set in the n8n UI (required to run)
These values are **not stored inside the JSON export**, so they must be selected manually in n8n:

- **Credentials:** Google Gemini (PaLM) API credential  
- **Model:** Default (recommended)

#### Why this node is used
- The AI Agent needs a language model to **think, reason, and write responses**.
- This node is the actual “brain” behind the agent.

#### If you remove this node
- The AI Agent has no model connected.
- The workflow will fail because the agent cannot run.

---

### Node 6: Edit Fields1 (Clean output)
**Type:** `Set (n8n-nodes-base.set)`

**Purpose:** Cleans the AI output so it can be parsed as JSON reliably.

#### Configuration (actual values)
| Output field | Expression (actual) | What it does |
|---|---|---|
| `cleanJson` | `={{ $json.output.replace(/```json\\n/g, '').replace(/```/g, '').replace(/\\\\n/g, '').trim() }}` | Removes markdown code blocks and extra escaped newlines |

| Setting | Value |
|---|---|
| **Options** | `{}` |

#### Why this node is used
- Even when instructed, AI models sometimes wrap output in ```json code blocks.
- This node removes formatting that would break JSON parsing.
- It makes live demos more reliable.

#### If you remove this node
- The next step (`JSON.parse`) may fail.
- The workflow can stop unexpectedly during execution.

---

### Node 7: Code in JavaScript (Parse JSON)
**Type:** `Code (n8n-nodes-base.code)`

**Purpose:** Converts cleaned JSON text into real structured fields.

#### Configuration (actual values)
javascript
const text = $input.first().json.cleanJson;
const cleanText = text.replace(/\\n/g, '').replace(/\n/g, '');
return [{ json: JSON.parse(cleanText) }];

### Node 8: Append row in sheet
**Type:** `Google Sheets (n8n-nodes-base.googleSheets)`

**Purpose:** Stores the AI screening result in a Google Sheet so a recruiter can review, compare, and take action later.

#### Configuration (actual values)
| Field | Value |
|---|---|
| **Operation** | `append` |
| **Spreadsheet** | `n8n-resume-screening-specialist` |
| **Sheet** | `Sheet1` |
| **Spreadsheet ID** | `1wuwA1N8zodzFsr7-xAdkPlY3BrCaJFiyv9osYryRqZ4` |
| **Sheet GID** | `gid=0` |
| **Options** | `{}` |

#### Column mapping (actual values)
| Column | Value |
|---|---|
| Timestamp | `={{ $now }}` |
| Candidate_Name | `={{ $json.candidate_name }}` |
| Match_Score | `={{ $json.match_score }}` |
| Key_Skills_Match | `={{ $json.key_skills_match }}` |
| Experience_Match | `={{ $json.experience_match }}` |
| Strengths | `={{ $json.strengths }}` |
| Gaps | `={{ $json.gaps }}` |
| Recommendation | `={{ $json.recommendation }}` |

#### Why this node is used
- Creates a **persistent audit trail** of all screened resumes.
- Allows HR teams to **review, sort, and compare** candidates.
- Keeps a **human-in-the-loop**, instead of auto-decisions.
- Makes the workflow usable in real HR operations.

#### If you remove this node
- The AI will still generate an evaluation.
- **Nothing is saved** for later review.
- The workflow becomes a one-off analysis instead of a usable process.

