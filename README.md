# 🤖 n8n Job Search AI Agent

> **Built by Anil Kumar Ramavath** | IOC Analyst @ NTT DATA, Hyderabad  
> Powered by **n8n** · **Google Gemini** · **Telegram** · **Serper.dev**

[![n8n](https://img.shields.io/badge/n8n-v2.21.7-orange)](https://n8n.io)
[![Gemini](https://img.shields.io/badge/Google%20Gemini-2.5-blue)](https://ai.google.dev)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Telegram-blue)](https://telegram.org)

---

## 📖 Overview

This project contains **two production-ready n8n automation workflows** built on **May 25, 2026**, designed to automate the job search process end-to-end:

| Workflow | Description |
|---|---|
| 🤖 **Job Search AI Agent v2** | Telegram-controlled AI agent that searches jobs, remembers your preferences, and replies with results |
| 📧 **Gmail Job Tracker** | Monitors Gmail for job-related emails and tracks them automatically |

The system was built entirely on a cloud Linux server, integrated with Google Gemini AI, Telegram Bot, and the Serper Google Search API — all orchestrated through n8n's visual workflow automation platform.

---

## 🏗️ Architecture

### Workflow 1: Job Search AI Agent v2

```
Telegram User
     │
     ▼
[Telegram Trigger]  ──────────────────────────────────────────────────────►
                                                                            │
                    [AI Agent (Google Gemini 2.5)]                          │
                         │              │              │                    │
                    [Chat Model]   [Simple Memory]  [HTTP Tool]            │
                   (Gemini 2.5)  (per-user session) (Serper.dev)           │
                                                                            │
                    [Send a text message]  ◄────────────────────────────────
                         │
                         ▼
                    Telegram Reply
```

**Flow:**
1. User sends a message to the Telegram Bot (e.g., "Find DevOps jobs in Hyderabad")
2. `Telegram Trigger` node receives the message
3. `AI Agent` (Google Gemini) interprets the intent
4. `Simple Memory` recalls user preferences (skills, location, job type)
5. `HTTP Request Tool` queries Serper.dev (Google Search API) for live job listings
6. AI formats the results and `Send a text message` delivers reply to Telegram

---

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|---|---|---|
| **Automation Platform** | n8n v2.21.7 | Workflow orchestration |
| **AI Model** | Google Gemini 2.5 | Natural language understanding & response |
| **Messaging** | Telegram Bot API | User interface & delivery |
| **Job Search API** | Serper.dev (Google Search) | Real-time job listings |
| **Memory** | n8n Simple Memory | Per-user session persistence |
| **Infrastructure** | Linux cloud server + ngrok | Hosting & tunneling |
| **Email Tracking** | Gmail API | Job application email monitoring |

---

## 📋 Prerequisites

Before you begin, ensure you have:

- [ ] **n8n** installed (self-hosted or cloud)
- [ ] **Telegram Bot Token** — Create via [@BotFather](https://t.me/BotFather)
- [ ] **Google Gemini API Key** — Get from [Google AI Studio](https://aistudio.google.com/app/apikey)
- [ ] **Serper.dev API Key** — Free tier at [serper.dev](https://serper.dev) (2,500 free searches/month)
- [ ] **Gmail OAuth** credentials (for Gmail Job Tracker workflow)

---

## 🚀 Setup Instructions

### Step 1: Clone or Import the Workflow

1. Download the workflow JSON from the `/workflows` folder
2. In n8n, go to **Workflows → Import from File**
3. Select the downloaded JSON file

### Step 2: Configure Telegram Bot

```bash
# 1. Open Telegram and search for @BotFather
# 2. Send: /newbot
# 3. Choose a name: e.g., "JobSearchBot"
# 4. Choose a username: e.g., "MyJobSearchBot"
# 5. Copy the API token provided
```

In n8n:
- Go to **Credentials → New → Telegram**
- Paste your Bot Token
- Save as "Telegram account"

### Step 3: Configure Google Gemini

```bash
# 1. Go to https://aistudio.google.com/app/apikey
# 2. Create a new API key
# 3. Copy the key
```

In n8n:
- Go to **Credentials → New → Google Gemini(PaLM) Api**
- Paste your API key
- Save as "Google Gemini account"

### Step 4: Configure Serper.dev

```bash
# 1. Sign up at https://serper.dev
# 2. Copy your API key from the dashboard
```

In n8n:
- In the HTTP Request tool node, set the `X-API-KEY` header to your Serper key

### Step 5: Set Per-User Memory

The memory node uses the Telegram Chat ID as the session key:
```
Session ID Expression: {{ $('Telegram Trigger').item.json.message.chat.id }}
```

### Step 6: Configure the AI Agent System Prompt

Set the system prompt in the AI Agent node:
```
You are a professional Job Search Assistant for Anil Kumar Ramavath.
You help find relevant job listings based on skills and location preferences.
When searching for jobs, use the HTTP Request tool with relevant search queries.
Format results clearly with: Job Title, Company, Location, and Apply Link.
Remember user preferences across the conversation using memory.
Always respond in a helpful, professional tone.
```

### Step 7: Activate the Workflow

1. Click **Publish** in n8n
2. The workflow will now listen for Telegram messages
3. Send a test message to your bot!

---

## 💬 Telegram Commands

Send these messages to your bot:

| Command | Example | Description |
|---|---|---|
| Search jobs | `Find DevOps Engineer jobs in Hyderabad` | Search for specific role + location |
| Set preferences | `I am a Cloud Engineer with Azure skills` | Save your profile |
| Remote jobs | `Find remote Python developer jobs` | Search remote positions |
| Daily digest | `Show me today's Azure jobs` | Get daily job updates |
| Specific companies | `Find jobs at Microsoft India` | Company-specific search |

---

## 📁 Repository Structure

```
n8n-job-search-ai-agent/
├── README.md                          # This file
├── LICENSE                            # MIT License
├── workflows/
│   ├── job-search-ai-agent-v2.json   # Main AI agent workflow
│   └── gmail-job-tracker.json        # Gmail monitoring workflow
├── screenshots/
│   ├── job-search-agent-workflow.png # Workflow canvas screenshot
│   └── gmail-tracker-workflow.png    # Gmail workflow screenshot
└── docs/
    └── SETUP_GUIDE.md                # Detailed setup instructions
```

---

## 🔐 Security Notes

- **Never commit** your API keys or bot tokens to GitHub
- Use n8n's built-in **Credentials** system to store secrets securely
- Rotate API keys if accidentally exposed
- The Serper.dev free tier provides 2,500 searches/month — monitor usage
- Google Gemini free tier has rate limits — upgrade if needed for production

---

## 🧠 How the AI Memory Works

The workflow uses **per-user session memory** keyed by Telegram Chat ID:

```javascript
// Session ID expression in Simple Memory node:
{{ $('Telegram Trigger').item.json.message.chat.id }}
```

This means:
- Each Telegram user gets their own isolated memory
- The AI remembers skills, location, job type preferences
- Memory persists across multiple conversations in the same session
- No data leakage between different users

---

## 🔄 Switching AI Models

If you hit rate limits on Gemini, switch models in the Chat Model node:

| Model | Best For | Rate Limit |
|---|---|---|
| `gemini-2.5-flash` | Fast responses, free tier | 15 RPM |
| `gemini-2.5-pro` | Complex reasoning | Lower free limit |
| `gemini-1.5-flash` | Stable, widely available | Higher free limit |

---

## 📊 Live Demo Output

When working correctly, a message like `Find Cloud Engineer jobs in Hyderabad` returns:

```
Sure, Anil! Based on your preferred roles (Cloud Engineer, DevOps Engineer, 
Azure Engineer, SRE) and location (Hyderabad), I can help you find some jobs.

I'll start by searching for **Cloud Engineer** roles in **Hyderabad**. 
Would you like me to look for remote roles as well, or focus on a 
different role first?
```

---

## 🤝 Author

**Anil Kumar Ramavath**  
IOC Analyst | NTT DATA, Hyderabad, India  
Skills: Azure Active Directory · Microsoft Entra ID · M365 · O365 · ITIL · Cloud Architecture  

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://www.linkedin.com/in/anil-kumar-ramavath-bb0128239)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black)](https://github.com/anil-ramavath)

---

## 📅 Project Timeline

| Date | Milestone |
|---|---|
| May 25, 2026 | Project started — n8n setup on cloud server |
| May 25, 2026 | Telegram Bot configured via BotFather |
| May 25, 2026 | Google Gemini AI model integrated |
| May 25, 2026 | Serper.dev job search API connected |
| May 25, 2026 | Per-user memory with session IDs implemented |
| May 25, 2026 | End-to-end workflow tested and published |
| May 25, 2026 | Gmail Job Tracker workflow created |
| May 25, 2026 | Repository created and documented on GitHub |

---

## 📜 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

*Built with ❤️ in Hyderabad, India on May 25, 2026*
