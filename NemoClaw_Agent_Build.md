# 🤖 Kinsey — A Locally-Hosted Agentic AI Built with NVIDIA NemoClaw

> **A full end-to-end build log of deploying a personal agentic AI assistant on consumer hardware using NVIDIA NemoClaw, OpenClaw, Docker, WSL2, Ollama, and Google Gemini — with live web search, Telegram bot integration, custom agent identity, and a secured sandbox execution environment.**

---

## 📌 Project Overview

This project documents the complete installation, configuration, troubleshooting, and customization of **Kinsey** — a personal AI agent built on **NVIDIA's NemoClaw** framework, running locally on a Windows 11 machine through WSL2. Kinsey is capable of reading and writing files, executing shell commands, managing background processes, searching the web, fetching web content, controlling a browser, managing scheduled tasks, and interfacing with external messaging platforms like Telegram.

The agent runs inside a sandboxed Docker container managed by the **OpenClaw** gateway — a production-grade, policy-enforced execution environment developed by NVIDIA. The project demonstrates hands-on experience with agentic AI architecture, local LLM deployment, containerization, cloud API integration, network security policy, and bot development.

**This project was completed entirely independently on personal hardware with no guided environment — every obstacle was researched and resolved from scratch.**

---

## ✅ What Was Built

- A fully operational **agentic AI** running on local hardware
- A **Telegram bot** (`@KinsroseBot`) that receives messages and responds in real time using AI inference
- An **OpenClaw web dashboard** accessible locally at `127.0.0.1:18789` for direct agent interaction
- A complete **agent workspace** with SOUL.md, IDENTITY.md, AGENTS.md, USER.md, and MEMORY.md configuration files
- **Live web search** powered by the Brave Search API
- **Multi-model inference** support — tested with `qwen2.5:7b`, `qwen2.5:3b`, `gemma4:latest`, `qwen3.5:9b`, and **Google Gemini 2.5 Flash**
- **Network security policy** (OPA-based sandboxing) with `Balanced` tier and custom binary allowlists
- A **GitHub repository** to document and share the project

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Host OS | Windows 11 |
| Linux Environment | Ubuntu via WSL2 (also Debian, openSUSE) |
| Containerization | Docker Desktop v4.71.0 |
| AI Agent Framework | NVIDIA NemoClaw + OpenClaw v2026.4.29 |
| Local LLM Inference | Ollama (qwen2.5:7b, qwen2.5:3b, gemma4:latest, qwen3.5:9b) |
| Cloud Inference | Google Gemini 2.5 Flash (via Gemini API) |
| Web Search | Brave Search API |
| Messaging | Telegram Bot (@KinsroseBot via BotFather) |
| Network Policy | OpenClaw OPA (Open Policy Agent), Balanced tier |
| Cloud Platform | Google Cloud (Gemini API + billing) |
| Version Control | GitHub |

---

## 🖥️ Hardware Specs

| Component | Spec |
|---|---|
| CPU | 12-core processor (MSI workstation) |
| RAM | ~8 GB (expanded with 4 GB swap file) |
| GPU | NVIDIA GPU — 6,144 MB VRAM |
| Storage | ~1 TB NVMe |
| OS | Windows 11 + WSL2 Ubuntu |

> **Note:** GPU VRAM (6 GB) was below the threshold for NVIDIA NIM (16+ GB required), so **Ollama** was used as the local inference engine, with **Google Gemini** as the cloud fallback.

---

## 📋 Installation & Configuration Walkthrough

### Phase 1 — Environment Setup

WSL2 was configured with multiple Linux distributions (Ubuntu, Debian, openSUSE). Docker Desktop required an admin-privilege reinstall after this error:

```
For security reasons C:\ProgramData\DockerDesktop must be owned by an elevated account
```

**Fix:** Right-click the Docker Desktop installer → **Run as Administrator**. This corrects directory ownership and completes installation normally.

After fix: Docker Desktop v4.71.0 running with 12 CPUs and 7.5 GB RAM available to containers.

---

### Phase 2 — NemoClaw Onboarding (8-Step Wizard)

#### [1/8] Preflight Checks
```
✓ Docker is running
✓ Container DNS resolution works
✓ Container runtime: docker-desktop
✓ openshell CLI: openshell 0.0.36
✓ Port 8080 available
✓ NVIDIA GPU detected: 1 GPU(s), 6144 MB VRAM
ⓘ Local NIM unavailable – GPU VRAM too small
✓ 4 GB Swap file created and activated
```

#### [2/8] OpenShell Gateway
```
Using pinned image: ghcr.io/nvidia/openshell/cluster:0.0.36
[~70 second startup]
✓ Gateway is healthy
✓ Active gateway set to 'nemoclaw'
```

#### [3/8] Inference Configuration
Chose **Local Ollama (localhost:11434)**. Initial model: `qwen2.5:7b` (~4.7 GB at 47 MB/s).

#### [4/8] Inference Provider
```
Route: inference.local | Provider: ollama-local | Model: qwen2.5:7b | Timeout: 180s
✓ Inference route set: ollama-local / qwen2.5:7b
```

#### [5/8] Telegram Messaging
Created `@KinsroseBot` via Telegram `@BotFather`. Token and user ID saved into the wizard.

#### [6/8] Sandbox Build
56-step multi-stage Docker build (~6 minutes):
```
✓ Created provider nemomsi-telegram-bridge
✓ Created provider nemomsi-brave-search
```

#### [7/8] Network Security Policy
OPA-based `Balanced` tier with presets: npm, pypi, huggingface, brew, brave, github, local-inference, telegram.

#### [8/8] Dashboard Access
```
Dashboard: http://127.0.0.1:18789/#token=<auth-token>
VS Code/WSL: http://172.18.121.21:18789/#token=<auth-token>
```
> ⚠️ **Critical:** This token is only printed **once**. Save it immediately in a secure location (a local text file or password manager). If you lose it, you will need to re-run the onboarding wizard.

---

## 📁 Agent Workspace Files — The "Soul" of Your Agent

One of the most important and often overlooked parts of NemoClaw is the **agent workspace** — a set of Markdown files stored at `/sandbox/.openclaw/workspace/` that collectively define who your agent *is*, how it *behaves*, what it *remembers*, and who it *talks to*.

When you first open the OpenClaw dashboard and navigate to **Agents**, you will see these files listed — often all marked as **MISSING**. This is normal after a fresh install. These files do not exist until you create them. Until they are populated, your agent will have no persistent identity, no behavioral rules, no memory, and no understanding of who you are.

Creating these files is what transforms a generic AI chatbot into a **personalized, purposeful agent**.

---

### 📄 SOUL.md — The Agent's Core Personality

**Path:** `/sandbox/.openclaw/workspace/SOUL.md`

SOUL.md is the agent's **fundamental character definition**. It is injected into the agent's context at the start of every session and shapes how the agent responds, reasons, and presents itself. Think of it as the agent's constitution — the rules it follows before anything else.

**What to put in SOUL.md:**
- The agent's name and how it refers to itself
- Its communication style (formal/casual, concise/verbose, technical/approachable)
- Core values and priorities (e.g., "always prioritize accuracy over speed")
- Behavioral constraints (e.g., "never make up information — say you don't know")
- Role definition (e.g., "you are a cybersecurity assistant focused on helping with defensive security")
- Tone guidance (e.g., "be direct and confident, but warm and encouraging")

**Example SOUL.md:**
```markdown
# Kinsey — Soul Configuration

You are Kinsey, a personal AI assistant and cybersecurity expert.

## Core Traits
- Direct and confident, but warm and supportive
- Always honest — if you don't know something, say so clearly
- Security-minded by default: assume least-privilege, verify before acting
- Patient teacher: explain concepts clearly, offer examples

## Communication Style
- Match the user's tone — casual when they're casual, precise when they're technical
- Keep responses concise unless depth is requested
- Use bullet points and code blocks for technical content

## Constraints
- Never fabricate facts, links, or credentials
- Always confirm before executing destructive actions
- Flag potential security concerns proactively
```

**Why it matters:** Without SOUL.md, the agent has no consistent identity. It may give different names, personalities, or behaviors across sessions. In testing, asking "what is your name?" returned "I'm an AI assistant" rather than "Kinsey" — because SOUL.md had not yet been written. Once SOUL.md was created with the correct name and identity, the agent responded consistently.

---

### 📄 IDENTITY.md — Persona Metadata

**Path:** `/sandbox/.openclaw/workspace/IDENTITY.md`

IDENTITY.md is a structured metadata file that stores the agent's **display identity** — its name, persona description, vibe, and avatar image. This is what the OpenClaw dashboard uses to populate the agent's name in the chat UI and what the Telegram bot uses as context for introductions.

**Example IDENTITY.md (Kinsey's configuration):**
```markdown
# IDENTITY.md - Who Am I?

- **Name:** Kinsey
- **Creature:** AI assistant and Cybersecurity expert — relentless in the pursuit of knowledge and loyal mentor
- **Vibe:** Warm, direct, security-minded. Technical teacher. INTJ-compatible.
- **Avatar:** https://raw.githubusercontent.com/JakeDeha/kinsey-assets/main/avatar.png
```

**Why it matters:** IDENTITY.md is what the OpenClaw dashboard reads to label the agent in the chat interface. Without it, the dashboard will show a generic fallback label. The avatar URL must be publicly accessible — hosting it on GitHub (raw content URL) works well.

> **Tip:** IDENTITY.md and SOUL.md work together. IDENTITY.md handles *who the agent presents as*; SOUL.md handles *how the agent actually behaves*. Both are needed.

---

### 📄 AGENTS.md — Multi-Agent Orchestration

**Path:** `/sandbox/.openclaw/workspace/AGENTS.md`

AGENTS.md defines how your agent interacts with **other agents** in a multi-agent setup. NemoClaw supports spawning sub-agents, delegating tasks to specialized agents, and routing requests between multiple sandboxes. AGENTS.md is where you define those relationships.

**What to put in AGENTS.md:**
- Names and roles of any sub-agents the main agent can call
- Routing rules (e.g., "route all code tasks to the coding agent")
- Coordination protocols (e.g., "always confirm with the user before spawning a sub-agent")
- Trust levels between agents

**Example AGENTS.md:**
```markdown
# Agent Roster

## main (this agent)
- Role: General assistant and orchestrator
- Can spawn: research-agent, code-agent

## research-agent
- Role: Web research and summarization
- Tools: brave-search, web-fetch
- Trigger: When user asks for current events, news, or research

## code-agent
- Role: Code writing, review, and debugging
- Tools: shell, file-read, file-write
- Trigger: When user asks for code generation or review
```

**Why it matters:** Without AGENTS.md, your agent operates as a standalone single agent and has no context for how to delegate or collaborate. If you plan to build a pipeline of specialized agents (e.g., a research agent feeding into a writing agent), AGENTS.md is where that architecture is defined.

---

### 📄 USER.md — Who Is the Human?

**Path:** `/sandbox/.openclaw/workspace/USER.md`

USER.md tells the agent **who it is talking to**. It contains information about you — your name, your role, your preferences, your technical background — so the agent can personalize its responses appropriately without you having to re-explain yourself every session.

**What to put in USER.md:**
- Your name and how you prefer to be addressed
- Your technical background (e.g., "intermediate programmer, comfortable with Python and Linux")
- Your role or occupation (e.g., "cybersecurity student at WGU")
- Communication preferences (e.g., "I prefer concise answers with code examples")
- Common tasks or projects (e.g., "I often work on network security labs and resume writing")
- Time zone or location if relevant for scheduling

**Example USER.md:**
```markdown
# User Profile

- **Name:** Jacob
- **Role:** Cybersecurity student (WGU), aspiring SOC analyst
- **Technical Level:** Intermediate — comfortable with Linux, Python basics, networking concepts
- **Preferences:** Concise answers with examples. Don't over-explain basics unless I ask.
- **Common Tasks:** Security labs, resume and cover letter writing, AI project documentation
- **Communication Style:** Direct. I appreciate honesty over politeness.
```

**Why it matters:** Without USER.md, the agent treats every conversation as if it's meeting you for the first time. With USER.md, it can greet you by name, skip unnecessary explanations, and tailor responses to your skill level and goals. This dramatically improves the quality and relevance of responses over time.

---

### 📄 MEMORY.md — Persistent Knowledge Across Sessions

**Path:** `/sandbox/.openclaw/workspace/MEMORY.md`

MEMORY.md is the agent's **long-term memory store**. Unlike conversation history (which is lost when a session ends), MEMORY.md persists between restarts and allows the agent to remember important facts, decisions, and context from previous interactions.

The agent can be instructed to update MEMORY.md automatically as it learns new things about you, your projects, or your preferences — or you can update it manually.

**What to put in MEMORY.md:**
- Important facts the agent should never forget (e.g., "User's primary project is called Kinsey-bot")
- Decisions made in past sessions (e.g., "We decided to use Gemini over Ollama for production")
- Ongoing projects and their current status
- User preferences discovered over time
- Key configuration details (e.g., "Telegram bot token is stored in ~/.nemoclaw/config")

**Example MEMORY.md:**
```markdown
# Kinsey Memory Log

## Active Projects
- **NemoClaw Agent (Kinsey):** Running on WSL2 + Docker. Using Gemini 2.5 Flash. Telegram bot: @KinsroseBot
- **GitHub Portfolio:** Repository at github.com/JakeDeha documenting the Kinsey build

## Key Decisions
- Switched from Ollama (qwen2.5:7b) to Google Gemini 2.5 Flash for production inference (May 2026)
- Chose Balanced OPA policy tier — Open tier was too permissive
- Dashboard token saved in ~/nemoclaw-token.txt

## User Preferences
- Prefers code blocks for all technical instructions
- Wants bullet-point summaries before detailed explanations
- Works best in the morning (Mountain Time)

## Known Issues
- Sandbox must be recreated after system reboot (resume mode handles this automatically)
- Gemini API key is rate-limited on free tier — use paid key for production
```

**Why it matters:** Without MEMORY.md, the agent "forgets" everything between sessions. You will find yourself re-explaining your projects, preferences, and context every single time. With MEMORY.md, the agent picks up exactly where you left off. This is what separates a chatbot from a true personal AI assistant.

---

### 📄 HEARTBEAT.md — Autonomous Background Tasks

**Path:** `/sandbox/.openclaw/workspace/HEARTBEAT.md`

HEARTBEAT.md controls what the agent does **on its own**, without being prompted. OpenClaw can trigger the agent on a schedule (using cron jobs), and HEARTBEAT.md defines what the agent should check or do during those autonomous cycles.

**Example HEARTBEAT.md:**
```markdown
# Heartbeat Instructions

On each heartbeat cycle:
1. Check MEMORY.md for any flagged tasks or reminders
2. If today matches a scheduled task date, notify the user via Telegram
3. Check if there are any pending messages in the Telegram queue
4. If nothing needs attention, reply: HEARTBEAT_OK

Do not repeat tasks from prior heartbeats. Do not infer tasks not listed here.
```

**Why it matters:** HEARTBEAT.md enables your agent to be **proactive** rather than purely reactive. Without it, the agent only responds when you message it. With it, the agent can remind you of deadlines, summarize news, check system status, or send you daily briefings automatically.

---

### Summary: The Full Workspace Picture

```
/sandbox/.openclaw/workspace/
├── SOUL.md        ← Who the agent IS (personality, values, rules)
├── IDENTITY.md    ← How the agent presents (name, avatar, vibe)
├── AGENTS.md      ← Multi-agent roles and routing
├── USER.md        ← Who the human IS (preferences, background)
├── MEMORY.md      ← What the agent remembers across sessions
├── HEARTBEAT.md   ← What the agent does autonomously on a schedule
├── TOOLS.md       ← Custom tool definitions and usage guidance
└── BOOTSTRAP.md   ← First-session initialization instructions
```

> **Pro Tip:** After a sandbox recreation (which happens on system restart), always verify your workspace files are still intact by opening the Agents panel in the OpenClaw dashboard. If files show as MISSING, the sandbox was wiped and you need to re-save them. Keeping backups of all workspace `.md` files in a separate folder on your host machine is strongly recommended.

---

## 🔧 Troubleshooting — Detailed Issue Log

### Issue 1: Docker Desktop Installation Fails (Ownership Error)

**Full Error:**
```
Docker Desktop installation failed.
For security reasons C:\ProgramData\DockerDesktop must be owned by an elevated account
```

**What's happening:** Windows created the `DockerDesktop` folder under your standard user account during a previous partial installation. Docker requires this folder to be owned by an administrator-level account, but it cannot fix the ownership itself during a non-elevated install.

**Step-by-step fix:**
1. Right-click the Docker Desktop installer → **Run as Administrator**
2. If the error persists, manually fix ownership:
   - Open PowerShell as Administrator
   - Run: `takeown /f "C:\ProgramData\DockerDesktop" /r /d y`
   - Run: `icacls "C:\ProgramData\DockerDesktop" /grant Administrators:F /t`
3. Re-run the installer as Administrator

**Verification:** After installation, open Docker Desktop and confirm it starts without errors. Check that WSL integration is enabled under Settings → Resources → WSL Integration for your Ubuntu distro.

---

### Issue 2: Gateway Token Mismatch

**Full Error (OpenClaw Dashboard):**
```
unauthorized: gateway token mismatch (open the dashboard URL and paste the token in Control UI settings)
```

**What's happening:** The OpenClaw gateway generates a unique auth token when the sandbox is first created. The dashboard at `127.0.0.1:18789` requires this token to authenticate your browser session. If you navigate directly to the URL without the token fragment, the dashboard rejects you.

**Step-by-step fix:**
1. Go back to your WSL terminal where you ran `nemoclaw onboard`
2. Scroll up through the output to find the line:
   ```
   Dashboard: http://127.0.0.1:18789/#token=0bedd62cd60ce00097d438931e8db45a76240ffd5c26dd95
   ```
3. Copy that **entire URL** (including `#token=...`) and paste it into your browser
4. The dashboard will authenticate and redirect you to the chat interface

**Prevention:** After onboarding completes, immediately save the full tokenized URL somewhere safe. The token is only printed **once** during the initial setup. On subsequent runs (resume mode), use `nemoclaw nemomsi connect` and then run `openclaw tui` inside the sandbox to get a fresh session — or check `~/.openclaw/` for stored credentials.

> **If you lost the token entirely:** Re-run `nemoclaw onboard` — it will detect the existing sandbox and offer a resume. If the gateway was restarted, a new token URL will be generated and printed.

---

### Issue 3: Telegram API Blocked by Sandbox Network Policy

**Symptoms:**
```
/core.telegram.org/bots/api △ Telegram reachability probe failed: curl failed (exit 3): URL rejected: Malformed input to a URL function
```
And in sandbox logs:
```
[sandbox] [OCSF] NET:OTHER [MED] — connection to api.telegram.org blocked
```

**What's happening:** The NemoClaw sandbox runs all agent traffic through an **OPA (Open Policy Agent)** network firewall. Even though you enabled Telegram during onboarding, the default binary allowlist may not include all the executables that the Node.js Telegram bridge needs to make outbound HTTPS connections. The `curl exit 3` error specifically means the URL was rejected before even reaching the network — it's a sandbox policy interception.

**Step-by-step fix:**

Step 1 — Export your current policy to a file:
```bash
openshell policy get nemomsi -g nemoclaw --full > ~/policy.yaml
```

Step 2 — Open the file in nano:
```bash
nano ~/policy.yaml
```

Step 3 — Navigate to the `telegram:` section (use `Ctrl+W`, type `telegram:`, press Enter)

Step 4 — After the last `path:` entry under `telegram`, add:
```yaml
binaries:
  - path: /usr/local/bin/node
  - path: /usr/bin/node
  - path: /usr/local/bin/openclaw
```

Step 5 — Repeat for the `telegram_bot:` section (use `Ctrl+W`, type `telegram_bot:`)

Step 6 — Save (`Ctrl+O`, Enter) and exit (`Ctrl+X`)

Step 7 — Apply the fixed policy:
```bash
openshell policy set nemomsi -g nemoclaw --policy ~/policy.yaml --wait
```

Step 8 — Verify by watching sandbox logs:
```bash
nemoclaw nemomsi logs --follow
```
You should now see:
```
HTTP:GET ALLOWED GET http://api.telegram.org:443/bot[CREDENTIAL]/getMe [policy:telegram]
HTTP:POST ALLOWED POST http://api.telegram.org:443/bot[CREDENTIAL]/getUpdates [policy:telegram]
```

**Why this happens:** The Balanced policy tier enables Telegram by domain, but it doesn't automatically whitelist every binary that might initiate that connection. Node.js is the runtime behind the NemoClaw Telegram bridge, and without explicitly allowing the Node binary path, the OPA engine blocks the outbound socket.

---

### Issue 4: Inference Timeouts (Local Ollama)

**Full Error (Telegram):**
```
Request timed out before a response was generated. Please try again, or increase agents.defaults.timeoutSeconds in your config.
```

**What's happening:** The `qwen2.5:7b` model (4.7 GB) running entirely on a 6 GB VRAM GPU is slow — typically 5–20 seconds per response depending on prompt complexity. Telegram's bot API has a default response expectation, and the NemoClaw agent has its own `timeoutSeconds` setting. If the model doesn't finish generating before the timeout, the request is killed and the error is returned.

**Fix Option 1 — Increase the timeout (temporary workaround):**

In your OpenClaw config or sandbox environment, increase `agents.defaults.timeoutSeconds`:
```bash
# Inside the sandbox (nemoclaw nemomsi connect)
openclaw config set agents.defaults.timeoutSeconds 300
```

**Fix Option 2 — Switch to a smaller model:**
```bash
ollama pull qwen2.5:3b
```
Then re-run `nemoclaw onboard` and select `qwen2.5:3b` when prompted for model selection. Smaller models respond significantly faster on limited VRAM.

**Fix Option 3 (Recommended for production) — Switch to Google Gemini:**

Re-run `nemoclaw onboard` and choose **Option 6 — Google Gemini** at the inference selection step. Gemini 2.5 Flash responds in 1–3 seconds and eliminates the timeout issue entirely. This requires a Google AI Studio API key (free tier available, paid tier recommended to avoid rate limits — see Issue 6).

**Root cause:** Consumer-grade GPUs with 6 GB VRAM are below the recommended spec for running 7B+ parameter models at production speed. The model runs, but slowly. Cloud inference via Gemini sidesteps this hardware limitation.

---

### Issue 5: Sandbox State Lost After System Restart

**Symptom:**
```
[resume] Recorded sandbox state is unavailable; recreating it.
Creating sandbox 'nemomsi' (this takes a few minutes on first run)...
```

**What's happening:** NemoClaw's sandbox is a **Docker container** — and Docker containers do not automatically restart when your PC reboots (unless configured to do so). When the container stops, its runtime state is lost. However, the underlying Docker **image** is cached, so recreation is much faster than the original build (seconds vs. minutes).

**Step-by-step recovery after restart:**
1. Open WSL2 Ubuntu terminal
2. Run: `nemoclaw onboard`
3. The wizard will detect cached preflight and gateway data and skip those steps
4. It will recreate only the sandbox container
5. You'll need to re-enter your Brave Search API key (stored config is in the sandbox, which was reset)
6. Messaging channels (Telegram) will be re-confirmed from saved config

**To prevent this — configure Docker to auto-restart the gateway container:**
```bash
docker update --restart=unless-stopped openshell-cluster
```
This tells Docker Desktop to restart the gateway container automatically when Windows starts. The sandbox container itself still needs `nemoclaw onboard` resume to reconnect, but the gateway will be available immediately.

**Recommendation:** After any successful full startup, note the port number in use (18789 or 18790) and save the new tokenized dashboard URL.

---

### Issue 6: Google Gemini Free Tier Rate Limiting

**Full Error (Telegram):**
```
⚠ All models are temporarily rate-limited. Please try again in a few minutes.
⚠ API rate limit reached. Please try again later.
```

**And in sandbox logs:**
```
[diagnostic] Error: ⚠ API rate limit reached. Please try again later.
[model-fallba...] Error: API rate limit reached.
```

**What's happening:** The Google Gemini API free tier (accessed via AI Studio) has strict rate limits: approximately 15 requests per minute and 1,500 requests per day on the free tier. An active Telegram bot can burn through these limits quickly, especially if the agent is making multiple tool calls per response (each tool call = an additional API request).

**Step-by-step fix — Upgrade to paid API access:**

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project (e.g., `My Project 8577` or `Nemoclaw-bot`)
3. Navigate to **Billing** → link a payment method to the project
4. Go to **APIs & Services** → **Library** → search for **Gemini API** → Enable it
5. Go to **APIs & Services** → **Credentials** → **Create API Key**
6. Copy the key
7. Re-run `nemoclaw onboard`, select **Google Gemini**, and paste the new key when prompted

**Alternative — Use a personal Gmail account (not a school/org account):**

If you're using a school or organization Google account (e.g., a `.edu` email), Google Cloud may block API key creation due to organizational security policies. You'll see: `API Keys are Disallowed — Your organization's security policy disallows API keys.`

**Fix:** Create a separate personal Google account at `gmail.com` and use that for Google AI Studio and Google Cloud. Personal accounts have full API key creation rights.

**Cost context:** Gemini 2.5 Flash on the paid tier is extremely affordable for personal use — typically less than $1/month for light to moderate usage. The free tier is sufficient for testing but not for a running Telegram bot.

---

### Issue 7: "SOUL.md" Not Read / Agent Responds with Wrong Name

**Symptom:** You ask "what is your name?" and the agent replies "I'm an AI assistant" or "You can call me Assistant" — ignoring what you put in SOUL.md or IDENTITY.md.

**What's happening:** There are two common causes:

**Cause A — The file is in the wrong location.** The workspace path must be exactly `/sandbox/.openclaw/workspace/SOUL.md`. If you created the file outside this directory (e.g., in `/home/jacob_2505/`), the agent will never read it.

**Cause B — The file hasn't been saved through the dashboard editor.** If you typed content into the OpenClaw dashboard file editor but didn't click **Save**, the file exists in the UI but hasn't been written to disk.

**Fix:**
1. In the OpenClaw dashboard, go to **Agents → Files**
2. Click **SOUL** (or **IDENTITY**) in the Core Files section
3. Verify the content is correct in the editor
4. Click **Save** (the red Save button in the top right of the editor)
5. Start a `/new` session in Telegram to clear the old context
6. Ask "what is your name?" again

**Additional note:** If the agent was already mid-session when you saved the file, it won't pick up the new identity until the session is reset. Always use `/new` after making workspace file changes.

---

### Issue 8: Port Conflict on Onboarding Resume

**Symptom:**
```
! Port 18789 is taken. Using port 18790 instead.
```

**What's happening:** A previous NemoClaw gateway is still running on port 18789, and the new onboarding run is trying to start a second gateway on the same port. Rather than killing the existing one, NemoClaw simply picks the next available port.

**Fix Option 1 — Use the new port:** Just update your bookmarked dashboard URL from `127.0.0.1:18789` to `127.0.0.1:18790`. The new tokenized URL will be printed in the terminal.

**Fix Option 2 — Kill the old gateway first:**
```bash
# Find the container using port 18789
docker ps | grep 8789
# Stop it
docker stop <container-id>
# Then re-run onboarding
nemoclaw onboard
```

**Prevention:** Before running `nemoclaw onboard`, check if a gateway is already running:
```bash
docker ps | grep openshell
```
If it's running and healthy, you may be able to skip onboarding entirely and just reconnect:
```bash
nemoclaw nemomsi connect
```

---

## 🏗️ Architecture Overview

```
┌──────────────────────────────────────────────────────────┐
│                     Windows 11 Host                       │
│                                                            │
│  ┌──────────────┐    ┌─────────────────────────────────┐  │
│  │  WSL2/Ubuntu │    │        Docker Desktop            │  │
│  │              │    │  ┌───────────────────────────┐   │  │
│  │  NemoClaw    │───▶│  │   OpenShell Gateway        │   │  │
│  │  CLI +       │    │  │   (openshell-cluster)      │   │  │
│  │  Ollama      │    │  │   Port: 8080:30051          │   │  │
│  │              │    │  └───────────┬───────────────┘   │  │
│  └──────────────┘    │              │                     │  │
│                      │  ┌───────────▼───────────────┐   │  │
│                      │  │   NemoClaw Sandbox          │   │  │
│                      │  │   Agent: Kinsey             │   │  │
│                      │  │                             │   │  │
│                      │  │  Workspace Files:           │   │  │
│                      │  │  ├── SOUL.md                │   │  │
│                      │  │  ├── IDENTITY.md            │   │  │
│                      │  │  ├── AGENTS.md              │   │  │
│                      │  │  ├── USER.md                │   │  │
│                      │  │  ├── MEMORY.md              │   │  │
│                      │  │  └── HEARTBEAT.md           │   │  │
│                      │  │                             │   │  │
│                      │  │  OPA Network Policy (Balanced)│  │  │
│                      │  └───────────────────────────┘   │  │
│                      └─────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
           │                   │                  │
           ▼                   ▼                  ▼
    ┌────────────┐   ┌────────────────┐   ┌────────────┐
    │  Telegram  │   │ Google Gemini  │   │   Brave    │
    │ @Kinsrose  │   │ 2.5 Flash API  │   │ Search API │
    └────────────┘   └────────────────┘   └────────────┘
```

---

## 🔑 Skills Demonstrated

- **Agentic AI Architecture** — Deployed a production-grade multi-component AI agent system from scratch on consumer hardware
- **Agent Persona Design** — Authored SOUL.md, IDENTITY.md, USER.md, MEMORY.md, and AGENTS.md workspace files to create a persistent, personalized agent identity
- **Linux / WSL2** — Multi-distro WSL2 environment, YAML editing in `nano`, process management, `ollama` and `openshell` CLI usage
- **Docker / Containerization** — Resolved Docker installation errors, managed containers, understood 56-step multi-stage Dockerfile builds
- **Network Security & Policy** — Manually modified OPA-based network security policies; configured binary allowlists for Telegram and external endpoints
- **API Integration** — Brave Search API, Telegram Bot API (BotFather), and Google Gemini API
- **Google Cloud Platform** — Set up GCP projects, enabled APIs, configured billing, navigated IAM and Agent Platform Studio
- **Debugging & Problem Solving** — Independently resolved 8 distinct technical issues across Docker, WSL, APIs, YAML policy, port conflicts, and auth
- **LLM Model Management** — Downloaded and benchmarked multiple Ollama models; understood quantization formats (q4_K_M); switched local↔cloud inference
- **Git / GitHub** — Created and published a public documentation repository

---

## 🧩 Full Troubleshooting Reference

| # | Issue | Root Cause | Fix |
|---|---|---|---|
| 1 | Docker Desktop ownership error | Installer run without admin rights | Run installer as Administrator |
| 2 | Gateway token mismatch | Navigated to dashboard without token fragment | Use full tokenized URL from terminal |
| 3 | Telegram API blocked by sandbox | Node.js binary not in OPA allowlist | Edit policy.yaml, add binary paths, reapply |
| 4 | Inference timeouts (Ollama) | 7B model too slow on 6 GB VRAM | Increase timeout, switch to smaller model, or use Gemini |
| 5 | Sandbox lost on restart | Docker containers don't persist across reboots | Use resume mode; set Docker restart policy |
| 6 | Gemini rate limiting | Free tier API limits exceeded | Enable Google Cloud billing + paid API key |
| 7 | Agent ignores SOUL/IDENTITY files | Wrong file path or file not saved | Verify path, click Save in dashboard, use /new |
| 8 | Port conflict on resume | Previous gateway still running on 18789 | Update URL to new port or kill old container first |

---

## 📦 Ollama Models Tested

| Model | Size | Notes |
|---|---|---|
| qwen2.5:7b | 4.7 GB | Initial — 100% GPU, slow responses |
| qwen2.5:3b | ~2 GB | Faster for local development |
| gemma4:latest | 9.6 GB | Downloaded for comparison |
| qwen3.5:9b-q4_K_M | 6.6 GB | Performance testing |
| **Google Gemini 2.5 Flash** | **Cloud** | **Final production model** |

---

## 🔗 References

- [NVIDIA NemoClaw GitHub](https://github.com/NVIDIA/NemoClaw)
- [OpenClaw Documentation](https://docs.openclaw.ai)
- [Ollama](https://ollama.com)
- [Brave Search API](https://brave.com/search/api/)
- [Google AI Studio](https://aistudio.google.com)
- [Google Cloud Console](https://console.cloud.google.com)
- [Telegram BotFather](https://t.me/BotFather)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [WSL2 Documentation](https://learn.microsoft.com/en-us/windows/wsl/)

---

## 👤 Author

**Jacob L. DeHart** | GitHub: [@JakeDeha](https://github.com/JakeDeha)

*Built May 2026 — All configuration, troubleshooting, and customization performed independently on personal consumer hardware.*
