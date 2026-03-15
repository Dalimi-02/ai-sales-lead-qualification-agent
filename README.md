
# AI Sales Lead Qualification Agent

An automated AI agent that qualifies incoming sales leads in real-time using **n8n**, **LLaMA 3.3** (via Groq), and conditional routing logic to notify teams or log data based on lead value.

## How It Works

```
Webhook → LLaMA 3.3 (Groq) → Parse Response → Route by Classification
                                                    ├── High Value  → Slack alert to sales team
                                                    ├── Medium Value → Log to Google Sheets
                                                    └── Low Value   → Auto-reply email to lead
                                                         ↓
                                                 Log all leads → Webhook Response
```

## Features

- Webhook-triggered pipeline — accepts lead data via HTTP POST
- LLM-based classification into **High**, **Medium**, or **Low** value using prompt-engineered inference
- Multi-branch conditional routing with separate actions per tier
- Real-time Slack notifications for high-value leads
- Google Sheets logging for audit trail
- Automated email reply for low-value leads
- End-to-end processing in under 3 seconds

## Tech Stack

| Tool | Purpose |
|------|---------|
| n8n | Workflow orchestration |
| Groq API + LLaMA 3.3 70B | Lead classification |
| Slack | Sales team notifications |
| Google Sheets | Lead logging |
| SMTP | Automated email replies |

## Usage

### 1. Import the workflow
Import `AI Sales Lead Qualification Agent.json` into your n8n instance.

### 2. Set credentials
Configure the following in n8n's credential manager:
- `Groq API Key` — [console.groq.com/keys](https://console.groq.com/keys)
- `Slack OAuth2` — Slack app with `chat:write` scope
- `Google Sheets OAuth2` — Service account or OAuth with Sheets access
- `SMTP` — Any SMTP provider (Gmail, Sendgrid, etc.)

### 3. Update placeholders
In the workflow, replace:
- `YOUR_GOOGLE_SHEET_ID` → your Google Sheet document ID
- `your-email@example.com` → your sender email address

### 4. Send a test lead

```bash
curl -X POST https://YOUR_N8N_INSTANCE/webhook/sales-lead \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Jane Smith",
    "email": "jane@enterprise.com",
    "company": "Acme Corp",
    "company_size": "500+",
    "message": "We need an AI solution for our sales team of 200 reps"
  }'
```

**Expected response:**
```json
{
  "success": true,
  "classification": "High Value",
  "reason": "Large enterprise with a specific, high-budget use case"
}
```

## Input Schema

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Lead's full name |
| `email` | string | Lead's email address |
| `company` | string | Company name |
| `company_size` | string | Number of employees |
| `message` | string | Lead's inquiry message |

## Classification Logic

The LLM evaluates leads based on company size, message intent, and urgency:

| Classification | Example Signal |
|---------------|---------------|
| High Value | Large company, clear budget, specific need |
| Medium Value | Mid-size company, exploring options |
| Low Value | Individual, vague inquiry, no company context |

## Author

**Dalimi Sonowal** — [github.com/Dalimi-02](https://github.com/Dalimi-02)
