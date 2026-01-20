## Step 2: Improve the workflow (PII redaction + safer recommendations)

In this step, we make two changes to the imported resume screening workflow to make it safer and more appropriate for HR usage.

---

## Change A: Add PII redaction before the AI Agent

### Why this change is needed
Resumes often contain personal information such as:
- Email address
- Phone number
- Year of birth
- Graduation year
- LinkedIn or portfolio links

This information is **not required** for skill-based screening and can introduce privacy and bias risks.

To reduce this risk, we redact obvious PII before sending the resume to the AI.

---

### New Node: Redact PII from Resume
**Type:** `Code (n8n-nodes-base.code)`

**Where to place this node**


---

### Node configuration
| Field | Value |
|---|---|
| **Mode** | Run Once for All Items |
| **Language** | JavaScript |

---

### JavaScript code

let text = $json.resumeText;

// Remove email addresses
text = text.replace(
  /[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}/gi,
  '[REDACTED_EMAIL]'
);

// Remove phone numbers
text = text.replace(
  /(\+?\d[\d\s\-]{7,}\d)/g,
  '[REDACTED_PHONE]'
);

// Remove years (DOB, graduation year – conservative)
text = text.replace(
  /\b(19|20)\d{2}\b/g,
  '[REDACTED_YEAR]'
);

// Remove URLs (LinkedIn, portfolio, photo links)
text = text.replace(
  /https?:\/\/\S+/gi,
  '[REDACTED_LINK]'
);

return [{
  json: {
    ...$json,
    resumeText: text
  }
}];

## Change B: Make AI Recommendations Safer (No Hire / Reject)

### Why this change is needed

Direct AI recommendations such as **hire** or **reject**:

- Trigger legal and ethical concerns
- Create fear of over-automation
- Reduce trust in AI-assisted screening

This workflow is designed to **support recruiters**, not replace human judgement.

---

### What you will change in this step

You will update the AI output so that it:
- Does **not** make final hiring decisions
- Uses softer, review-oriented language
- Clearly positions AI as an assistant

---

### Old recommendation style (do NOT use)

"recommendation": "Hire"
"recommendation": "Reject"

### New recommendation style (for use)
"recommendation": "Proceed to recruiter review"
"recommendation": "Needs clarification"
"recommendation": "Not aligned on must-haves"

### How to update the AI Agent prompt
In the AI Agent node, update the system or instruction prompt to include

You are assisting a recruiter with resume screening.
Do not make hiring decisions.
Use only the following recommendation values:
- Proceed to recruiter review
- Needs clarification
- Not aligned on must-haves
Your output is advisory, not final.

### What this change does
- Removes binary hire / reject decisions
- Encourages human review
- Makes the workflow safer to demo and deploy

### If you skip this change
- The AI may appear to replace recruiter judgement
- HR leaders may resist adoption
- Legal and compliance concerns may be raised
- Trust in the system may drop during demos

### Why this works better for HR teams
- Keeps humans in control
- Positions AI as an assistant, not a judge
- Matches real-world recruitment workflows
- Reduces resistance during live demos

