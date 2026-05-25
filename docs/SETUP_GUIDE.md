# 🛠️ Complete Setup Guide

## n8n Job Search AI Agent — Step-by-Step Setup

> **Author:** Anil Kumar Ramavath | IOC Analyst @ NTT DATA, Hyderabad  
> **Date:** May 25, 2026

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Install n8n](#install-n8n)
3. [Create Telegram Bot](#create-telegram-bot)
4. [Get Google Gemini API Key](#get-google-gemini-api-key)
5. [Get Serper.dev API Key](#get-serperdev-api-key)
6. [Import Workflow into n8n](#import-workflow-into-n8n)
7. [Configure Credentials in n8n](#configure-credentials-in-n8n)
8. [Set Up ngrok (for Webhooks)](#set-up-ngrok-for-webhooks)
9. [Publish and Test](#publish-and-test)
10. [Troubleshooting](#troubleshooting)

---

## 1. Prerequisites

Before starting, make sure you have:

- A Linux server or Windows/Mac machine
- Docker (recommended) or Node.js 18+
- Internet access
- A Telegram account
- A Google account (for Gemini API)

---

## 2. Install n8n

### Option A: Using Docker (Recommended)

```bash
# Pull and run n8n
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n

# Access at: http://localhost:5678
```

### Option B: Using npm

```bash
# Install n8n globally
npm install n8n -g

# Start n8n
n8n start

# Access at: http://localhost:5678
```

### Option C: Cloud (n8n.cloud)

Sign up at [n8n.cloud](https://n8n.cloud) for a managed instance.

---

## 3. Create Telegram Bot

```
1. Open Telegram on your phone or desktop
2. Search for: @BotFather
3. Send: /start
4. Send: /newbot
5. Enter a bot name: e.g., "Job Search Assistant"
6. Enter a username: e.g., "jobsearchhyd_bot" (must end in 'bot')
7. BotFather will give you a TOKEN like:
   1234567890:ABCdefGHIjklMNOpqrSTUVwxyz

8. SAVE THIS TOKEN — you'll need it in n8n
9. Find your bot by its username and start it (send /start)
```

**Important:** Never share your bot token publicly. Keep it secret.

---

## 4. Get Google Gemini API Key

```
1. Go to: https://aistudio.google.com/app/apikey
2. Sign in with your Google account
3. Click "Create API key"
4. Select your Google Cloud project (or create new)
5. Copy the API key: AIza...

6. SAVE THIS KEY — you'll need it in n8n
```

**Free Tier Limits:**
- Gemini 2.5 Flash: 15 requests/minute, 1M tokens/day
- Gemini 1.5 Flash: 15 RPM, higher daily limits

---

## 5. Get Serper.dev API Key

```
1. Go to: https://serper.dev
2. Click "Get started for free"
3. Sign up with your email
4. Dashboard will show your API key
5. Free tier: 2,500 searches/month

6. SAVE THIS KEY — you'll need it in n8n
```

---

## 6. Import Workflow into n8n

```
1. Open n8n in your browser: http://localhost:5678
2. Go to: Workflows (left sidebar)
3. Click "Add workflow" → "Import from file"
4. Upload: workflows/job-search-ai-agent-v2.json
5. The workflow will appear on the canvas
6. Repeat for: workflows/gmail-job-tracker.json
```

---

## 7. Configure Credentials in n8n

### Telegram Credential

```
1. In n8n, go to: Settings → Credentials → New
2. Search for: "Telegram"
3. Select: "Telegram API"
4. Paste your Bot Token
5. Name it: "Telegram account"
6. Click Save
7. Go back to the workflow
8. Click the Telegram Trigger node
9. Select "Telegram account" in the Credential field
10. Repeat for the "Send a text message" node
```

### Google Gemini Credential

```
1. In n8n, go to: Settings → Credentials → New
2. Search for: "Google Gemini"
3. Select: "Google Gemini(PaLM) Api"
4. Paste your Gemini API Key
5. Name it: "Google Gemini account"
6. Click Save
7. In the workflow, click "Google Gemini Chat Model" node
8. Select "Google Gemini account"
```

### Serper.dev API Key

```
1. In the workflow, click the "HTTP Request" tool node
2. Find the Header: X-API-KEY
3. Replace "REPLACE_WITH_YOUR_SERPER_API_KEY" with your actual key
4. Save the node
```

---

## 8. Set Up ngrok (for Webhooks)

The Telegram Trigger needs a public URL. Use ngrok to expose your local n8n:

```bash
# Install ngrok
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc
echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok

# Authenticate (get token from https://dashboard.ngrok.com)
ngrok authtoken YOUR_NGROK_TOKEN

# Start tunnel (run in separate terminal)
ngrok http 5678

# You'll get a URL like: https://abc123.ngrok-free.app
```

**In n8n environment:**

```bash
# Set the webhook URL before starting n8n
export WEBHOOK_URL=https://abc123.ngrok-free.app/

# Or add to n8n config
N8N_EDITOR_BASE_URL=https://abc123.ngrok-free.app
WEBHOOK_URL=https://abc123.ngrok-free.app/
```

---

## 9. Publish and Test

```
1. In n8n, open the Job Search AI Agent v2 workflow
2. Click "Publish" (top right)
3. Select "Publish workflow"
4. Workflow status should show green "Published"

5. Go to Telegram
6. Find your bot by username
7. Send: /start
8. Send: "Find DevOps Engineer jobs in Hyderabad"
9. Wait 5-10 seconds for the AI to respond
10. You should receive job listings in Telegram!
```

**Expected Response:**
```
Sure! I'll search for DevOps Engineer jobs in Hyderabad.

Here are the results:

1. DevOps Engineer - Tech Mahindra, Hyderabad
   Apply: https://...

2. Senior DevOps Engineer - Wipro, Hyderabad
   Apply: https://...
...
```

---

## 10. Troubleshooting

### ❌ "Telegram Trigger not receiving messages"

```
Cause: ngrok URL not set or expired
Fix:
1. Restart ngrok: ngrok http 5678
2. Update WEBHOOK_URL in n8n settings
3. Deactivate and reactivate the workflow
4. Check: n8n Settings → Tunneling
```

### ❌ "Google Gemini API error / rate limit"

```
Cause: Free tier rate limit (15 RPM)
Fix Option 1: Wait 60 seconds and retry
Fix Option 2: Switch model in Chat Model node:
  - Change to: gemini-1.5-flash (more stable free tier)
  
Fix Option 3: Upgrade to paid Gemini API plan
```

### ❌ "Serper API returning no results"

```
Cause: API key expired or free tier exhausted (2,500/month)
Fix:
1. Check usage at: https://serper.dev/dashboard
2. Generate a new API key
3. Update the X-API-KEY header in HTTP Request node
```

### ❌ "AI Agent not using the HTTP tool"

```
Cause: Tool description unclear
Fix: Update the toolDescription in HTTP Request node:
"Search for job listings on LinkedIn, Naukri, Indeed. 
Input a search query like 'DevOps Engineer Hyderabad 2026'"
```

### ❌ "Memory not working between messages"

```
Cause: Session key expression wrong
Fix: Verify the Session Key in Simple Memory node is:
{{ $('Telegram Trigger').item.json.message.chat.id }}
```

---

## Free API Limits Summary

| Service | Free Tier | Limit |
|---|---|---|
| Google Gemini 2.5 Flash | Yes | 15 RPM, 1M tokens/day |
| Serper.dev | Yes | 2,500 searches/month |
| Telegram Bot API | Yes | Unlimited |
| n8n (self-hosted) | Yes | Unlimited |
| ngrok (free) | Yes | 1 tunnel, HTTPS |

---

## Environment Variables Reference

```bash
# n8n Configuration
N8N_HOST=0.0.0.0
N8N_PORT=5678
N8N_PROTOCOL=http
WEBHOOK_URL=https://YOUR_NGROK_URL/
N8N_EDITOR_BASE_URL=https://YOUR_NGROK_URL

# Optional: n8n database (default: SQLite)
DB_TYPE=sqlite
DB_SQLITE_DATABASE_PATH=/home/node/.n8n/database.sqlite

# Security (optional but recommended)
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=yourpassword
```

---

*For more help, open an issue on GitHub or refer to the [n8n documentation](https://docs.n8n.io)*
