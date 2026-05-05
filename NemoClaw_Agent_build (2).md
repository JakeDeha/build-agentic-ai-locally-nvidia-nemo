# 🤖 Kinsey — A Locally-Hosted Agentic AI Built with NVIDIA NemoClaw

> A full end-to-end build log of deploying a personal agentic AI assistant on consumer hardware using NVIDIA NemoClaw, OpenClaw, Docker, WSL2, Ollama, and Google Gemini — with live web search, Telegram bot integration, custom agent identity, and a secured sandbox execution environment.

---

## 📌 Project Overview

This project documents the complete installation, configuration, troubleshooting, and customization of **Kinsey** — a personal AI agent built on **NVIDIA's NemoClaw** framework, running locally on a Windows 11 machine through WSL2. Kinsey is capable of reading and writing files, executing shell commands, managing background processes, searching the web, fetching web content, controlling a browser, managing scheduled tasks, and interfacing with external messaging platforms like Telegram.

The agent runs inside a sandboxed Docker container managed by the **OpenClaw** gateway — a production-grade, policy-enforced execution environment developed by NVIDIA.

> ⭐ **This project was completed entirely independently on personal hardware with no guided environment — every obstacle was researched and resolved from scratch.**

---

## ✅ What Was Built

- A fully operational **agentic AI** running on local hardware
- A **Telegram bot** (`@KinsroseBot`) that receives messages and responds in real time using AI inference
- An **OpenClaw web dashboard** accessible locally at `127.0.0.1:18789` for direct agent interaction
- A complete **agent workspace** with `SOUL.md`, `IDENTITY.md`, `AGENTS.md`, `USER.md`, `MEMORY.md`, and `HEARTBEAT.md`
- **Live web search** powered by the Brave Search API
- **Multi-model inference** — tested with `qwen2.5:7b`, `qwen2.5:3b`, `gemma4:latest`, `qwen3.5:9b`, and **Google Gemini 2.5 Flash**
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
| Local LLM Inference | Ollama (`qwen2.5:7b`, `qwen2.5:3b`, `gemma4:latest`, `qwen3.5:9b`) |
| Cloud Inference | Google Gemini 2.5 Flash (via Gemini API) |
| Web Search | Brave Search API |
| Messaging | Telegram Bot (`@KinsroseBot` via BotFather) |
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
Docker Desktop installation failed.
For security reasons C:\ProgramData\DockerDesktop must be owned by an elevated account
```

**Fix:** Right-click the Docker Desktop installer → **Run as Administrator**.

After fix: Docker Desktop v4.71.0 running with 12 CPUs and 7.5 GB RAM available to containers.

---

### Phase 2 — NemoClaw Onboarding (8-Step Wizard)

The NemoClaw onboarding wizard (`nemoclaw onboard`) ran an interactive guided setup from within the WSL2 Ubuntu terminal.

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

#### [2/8] Starting the OpenShell Gateway
```
Using pinned image: ghcr.io/nvidia/openshell/cluster:0.0.36
[~70 second startup]
✓ Gateway is healthy
✓ Active gateway set to 'nemoclaw'
```

#### [3/8] Inference Configuration
Chose **Local Ollama (localhost:11434)**. Initial model pull: `qwen2.5:7b` (~4.7 GB at 47 MB/s).

#### [4/8] Inference Provider
```
Route: inference.local | Provider: ollama-local | Model: qwen2.5:7b | Timeout: 180s
✓ Inference route set: ollama-local / qwen2.5:7b
```

#### [5/8] Telegram Messaging
Created `@KinsroseBot` via Telegram `@BotFather`. Token and user ID saved into the wizard.

```
✓ telegram token saved
✓ telegram user ID saved
```

#### [6/8] Sandbox Build — 56-Step Docker Build
```
Creating sandbox 'nemomsi' (this takes a few minutes on first run)...
Building sandbox image... [~6 minutes, 56 steps]
Step 1-56: Base image → Node.js builder → Python → OpenClaw plugins → policy configs
✓ Created provider nemomsi-telegram-bridge
✓ Created provider nemomsi-brave-search
```

#### [7/8] Network Security Policy
OPA-based `Balanced` tier. Enabled presets: `npm`, `pypi`, `huggingface`, `brew`, `brave`, `github`, `local-inference`, `telegram`.

#### [8/8] Dashboard Access
```
Dashboard: http://127.0.0.1:18789/#token=<auth-token>
VS Code/WSL: http://172.18.121.21:18789/#token=<auth-token>
```

> ⚠️ **Critical:** This token is only printed **once**. Save it immediately in a secure location. If lost, re-run `nemoclaw onboard` to generate a new one.

---

## 📁 Agent Workspace Files — The Soul of Your Agent

One of the most important and often overlooked parts of NemoClaw is the **agent workspace** — a set of Markdown files stored at `/sandbox/.openclaw/workspace/` that collectively define who your agent *is*, how it *behaves*, what it *remembers*, and who it *talks to*.

When you first open the OpenClaw dashboard and navigate to **Agents**, you will see these files listed — often all marked as **MISSING**. This is normal after a fresh install. Until they are populated, your agent has no persistent identity, no behavioral rules, and no memory.

> 💡 **Creating these files is what transforms a generic AI chatbot into a personalized, purposeful agent.**

### Workspace File Summary

| File | Purpose | Without It |
|---|---|---|
| `SOUL.md` | Core personality, values, tone, and behavioral rules | Agent has no consistent identity or character |
| `IDENTITY.md` | Display name, vibe description, and avatar image URL | Dashboard shows generic label; agent may give wrong name |
| `AGENTS.md` | Multi-agent roles, routing rules, and delegation logic | Agent cannot delegate or collaborate with sub-agents |
| `USER.md` | Who the human is — preferences, background, goals | Agent re-introduces itself every session, no personalization |
| `MEMORY.md` | Long-term persistent facts across sessions and restarts | Agent forgets everything between sessions |
| `HEARTBEAT.md` | Autonomous scheduled tasks and proactive behaviors | Agent is purely reactive — only responds, never initiates |
| `TOOLS.md` | Custom tool definitions and usage guidance | Agent uses only default tools with no custom behavior |
| `BOOTSTRAP.md` | First-session initialization and startup instructions | No guided startup sequence on first run |

---

### 📄 SOUL.md — The Agent's Core Personality

SOUL.md is the agent's **fundamental character definition**. It is injected into the agent's context at the start of every session and shapes how the agent responds, reasons, and presents itself. Think of it as the agent's constitution — the rules it follows before anything else.

**What to include:**
- The agent's name and how it refers to itself
- Communication style (formal/casual, technical/approachable)
- Core values and priorities
- Behavioral constraints
- Role definition and tone guidance

**Kinsey's actual SOUL.md (written from scratch):**

```markdown
# SOUL.md for Kinsey (OpenClaw instance)

## Persona Overview
**Name**: Kinsey

Identity: You are an expert in all things cybersecurity, equally comfortable
on both the offensive (red team) and defensive (blue team) sides of security.
Deep expertise across:

  - Threat Intelligence — TTPs, IOCs, emerging attack campaigns
  - Network Security — firewalls, IDS/IPS, traffic analysis, segmentation
  - Privacy & OpSec — anonymization, data minimization, privacy tools
  - Incident Response — triage, containment, eradication, recovery
  - Malware Analysis — static/dynamic analysis, IOC extraction
  - AppSec / Code Review — OWASP Top 10, SAST/DAST, secure coding
  - AI Security — adversarial attacks, model poisoning, prompt injection

Always communicates in a beginner-friendly way — breaking down complex
concepts into plain language, using real-world analogies. Approach security
from an ethical standpoint — discuss offensive techniques to help people
understand and defend, not to cause harm.

## Core Truths
  Automate or die. If doing it manually more than twice, script it.
  Infrastructure is code. Version control, code review, automated testing.
  Observability > guessing. Logs, metrics, traces. Instrument everything.
  Fail fast, recover faster. Plan for failure. Make failures boring.
  Think in systems. A deployment isn't just code hitting production.

## Boundaries
  - Private things stay private. Period.
  - When in doubt, ask before acting externally.
  - Never send half-baked replies to messaging surfaces.
  - You're not the user's voice — be careful in group chats.
  - Don't over-engineer. Not every problem needs Kubernetes.
  - Document your automation. Future-you will thank you.
  - When production's down, fix first, optimize later.

## Vibe
  Warm but grounded — care about the person, not just the problem.
  Straight shooter — honest answers even when not what they want to hear.
  Patient without being condescending. Never make anyone feel dumb.
  Encouraging — celebrate progress, no matter how small.
  Calm under pressure — slow down, think clearly, help the user do the same.
  Real talk over formality — skip the corporate language.
  Quietly confident — knowledge shows naturally, no flexing needed.

## Continuity
  Each session, you wake up fresh. These files ARE your memory.
  Read them. Update them. They are how you persist.
  Never make the user repeat themselves.
  Treat every conversation as part of a longer relationship.
  Track ongoing work and follow up proactively.
  Pay attention to how the user likes to receive information and adapt.
```

> ⚠️ **Without SOUL.md**, the agent has no consistent identity. In testing, asking *"what is your name?"* returned *"I'm an AI assistant"* rather than *"Kinsey"* — because SOUL.md had not yet been written and saved.

> 💡 **Note for recruiters:** The SOUL.md above was written entirely from scratch — not generated or copied from documentation. It reflects deliberate thinking about agent identity, security ethics, communication philosophy, and continuity design. Writing a SOUL.md well requires understanding both how LLMs process system-level instructions *and* what makes an AI agent genuinely useful and trustworthy in practice.

---

### 📄 IDENTITY.md — Persona Metadata

IDENTITY.md stores the agent's **display identity** — name, persona description, vibe, and avatar. This is what the OpenClaw dashboard uses to label the agent and what the Telegram bot uses for introductions.

```markdown
# IDENTITY.md - Who Am I?

- **Name:** Kinsey
- **Creature:** AI assistant and Cybersecurity expert — relentless in the
               pursuit of knowledge and loyal mentor
- **Vibe:** Warm, direct, security-minded. Technical teacher. INTJ-compatible.
- **Avatar:** https://raw.githubusercontent.com/JakeDeha/kinsey-assets/main/avatar.png
```

> 💡 IDENTITY.md and SOUL.md work together. IDENTITY.md handles *who the agent presents as*; SOUL.md handles *how the agent actually behaves*. Both are needed.

---

### 📄 AGENTS.md — Multi-Agent Orchestration

AGENTS.md defines how your agent interacts with **other agents**. NemoClaw supports spawning sub-agents, delegating tasks, and routing requests between sandboxes.

```markdown
# Agent Roster

## main (this agent)
- Role: General assistant and orchestrator
- Can spawn: research-agent, code-agent

## research-agent
- Role: Web research and summarization
- Tools: brave-search, web-fetch
- Trigger: When user asks for current events or research

## code-agent
- Role: Code writing, review, and debugging
- Tools: shell, file-read, file-write
- Trigger: When user asks for code generation or review
```

---

### 📄 USER.md — Who Is the Human?

USER.md tells the agent **who it is talking to** — your name, role, preferences, and technical background — so it can personalize responses without you re-explaining yourself every session.

```markdown
# User Profile

- **Name:** Jacob
- **Role:** Cybersecurity student (WGU), aspiring SOC analyst
- **Technical Level:** Intermediate — Linux, Python basics, networking concepts
- **Preferences:** Concise answers with examples. Don't over-explain basics.
- **Common Tasks:** Security labs, resume writing, AI project documentation
- **Communication Style:** Direct. Honesty over politeness.
```

> 💡 Without USER.md, the agent treats every conversation as if it's meeting you for the first time. With it, the agent greets you by name, skips unnecessary explanations, and tailors responses to your skill level.

---

### 📄 MEMORY.md — Persistent Knowledge Across Sessions

MEMORY.md is the agent's **long-term memory store**. Unlike conversation history (lost when a session ends), MEMORY.md persists between restarts.

```markdown
# Kinsey Memory Log

## Active Projects
- NemoClaw Agent (Kinsey): Running on WSL2 + Docker.
  Using Gemini 2.5 Flash. Telegram: @KinsroseBot
- GitHub Portfolio: github.com/JakeDeha

## Key Decisions
- Switched from Ollama (qwen2.5:7b) to Google Gemini 2.5 Flash (May 2026)
- Chose Balanced OPA policy tier — Open tier was too permissive
- Dashboard token saved in ~/nemoclaw-token.txt

## User Preferences
- Prefers code blocks for all technical instructions
- Wants bullet-point summaries before detailed explanations
```

> 💡 Without MEMORY.md, the agent "forgets" everything between sessions. This is what separates a chatbot from a true personal AI assistant.

---

### 📄 HEARTBEAT.md — Autonomous Background Tasks

HEARTBEAT.md controls what the agent does **on its own**, without being prompted. OpenClaw can trigger the agent on a schedule, and HEARTBEAT.md defines what it should do during those cycles.

```markdown
# Heartbeat Instructions

On each heartbeat cycle:
1. Check MEMORY.md for any flagged tasks or reminders
2. If today matches a scheduled task date, notify via Telegram
3. Check if there are any pending messages in the Telegram queue
4. If nothing needs attention, reply: HEARTBEAT_OK

Do not repeat tasks from prior heartbeats.
Do not infer tasks not listed here.
```

---

### Workspace File Structure

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

> 💡 **Pro Tip:** After a sandbox recreation (which happens on system restart), always verify your workspace files are still intact by opening the Agents panel in the OpenClaw dashboard. Keep backups of all workspace `.md` files on your host machine.

---

## 🔧 Troubleshooting — Detailed Issue Log

### Issue 1: Docker Desktop Installation Fails (Ownership Error)

**Full error:**
```
Docker Desktop installation failed.
For security reasons C:\ProgramData\DockerDesktop must be owned by an elevated account
```

**What's happening:** Windows created the `DockerDesktop` folder under a standard user account during a previous partial install. Docker requires this folder to be owned by an administrator account but cannot fix it during a non-elevated install.

**Step-by-step fix:**
```powershell
# Option 1 — Simply right-click installer → Run as Administrator

# Option 2 — If error persists, open PowerShell as Administrator:
takeown /f "C:\ProgramData\DockerDesktop" /r /d y
icacls "C:\ProgramData\DockerDesktop" /grant Administrators:F /t
# Then re-run the installer as Administrator
```

**Verify:** Open Docker Desktop → Settings → Resources → WSL Integration → confirm your Ubuntu distro is enabled.

---

### Issue 2: Gateway Token Mismatch

**Full error (OpenClaw dashboard):**
```
unauthorized: gateway token mismatch (open the dashboard URL and paste the token in Control UI settings)
```

**What's happening:** The OpenClaw gateway generates a unique auth token when the sandbox is created. Navigating to `127.0.0.1:18789` without the `#token=...` fragment causes this rejection. The dashboard can't authenticate your session without it.

**Step-by-step fix:**

1. Return to your WSL terminal where you ran `nemoclaw onboard`
2. Scroll up to find:
```
Dashboard: http://127.0.0.1:18789/#token=0bedd62cd60ce00097d438931e8db45a...
```
3. Copy the **entire URL** including `#token=...` and paste it into your browser
4. The dashboard authenticates and redirects you to the chat interface

> ⚠️ **Prevention:** The token is printed **only once**. Save the full tokenized URL immediately after onboarding. If lost, re-run `nemoclaw onboard` — a new token will be generated.

---

### Issue 3: Telegram API Blocked by Sandbox Network Policy

**Symptoms:**
```
/core.telegram.org/bots/api △ Telegram reachability probe failed:
curl failed (exit 3): URL rejected: Malformed input to a URL function

[sandbox] [OCSF] NET:OTHER [MED] — connection to api.telegram.org blocked
```

**What's happening:** The NemoClaw sandbox runs all agent traffic through an **OPA (Open Policy Agent)** network firewall. Even though Telegram was enabled during onboarding, the default policy may not whitelist the Node.js binaries that the Telegram bridge uses to make outbound HTTPS connections. `curl exit 3` means the URL was rejected by sandbox policy *before* even reaching the network.

**Step-by-step fix:**

```bash
# Step 1 — Export current policy
openshell policy get nemomsi -g nemoclaw --full > ~/policy.yaml

# Step 2 — Open in nano
nano ~/policy.yaml

# Step 3 — Find the telegram: section (Ctrl+W → type "telegram:" → Enter)
# Step 4 — After the last path: entry, add:
```
```yaml
binaries:
  - path: /usr/local/bin/node
  - path: /usr/bin/node
  - path: /usr/local/bin/openclaw
```
```bash
# Step 5 — Repeat for telegram_bot: section
# Step 6 — Save (Ctrl+O, Enter) and exit (Ctrl+X)

# Step 7 — Apply the fixed policy
openshell policy set nemomsi -g nemoclaw --policy ~/policy.yaml --wait

# Step 8 — Verify with sandbox logs
nemoclaw nemomsi logs --follow
# You should now see:
# HTTP:GET ALLOWED GET http://api.telegram.org:443/bot[CREDENTIAL]/getMe
# HTTP:POST ALLOWED POST http://api.telegram.org:443/bot[CREDENTIAL]/getUpdates
```

**Why this happens:** The Balanced policy tier enables Telegram by domain, but doesn't automatically whitelist every binary that may initiate that connection. Node.js is the runtime behind the NemoClaw Telegram bridge — without allowing its binary path, OPA blocks the outbound socket.

---

### Issue 4: Inference Timeouts (Local Ollama)

**Full error (Telegram):**
```
Request timed out before a response was generated.
Please try again, or increase agents.defaults.timeoutSeconds in your config.
```

**What's happening:** The `qwen2.5:7b` model (4.7 GB) running entirely on 6 GB VRAM is slow — typically 5–20 seconds per response. If the model doesn't finish before the timeout window, the request is killed and this error is returned.

**Fix Option 1 — Increase the timeout:**
```bash
# Inside the sandbox (nemoclaw nemomsi connect)
openclaw config set agents.defaults.timeoutSeconds 300
```

**Fix Option 2 — Switch to a smaller model:**
```bash
ollama pull qwen2.5:3b
# Then re-run nemoclaw onboard and select qwen2.5:3b
```

**Fix Option 3 (Recommended for production) — Switch to Google Gemini:**

Re-run `nemoclaw onboard` → choose **Option 6 — Google Gemini** at the inference step. Gemini 2.5 Flash responds in 1–3 seconds and eliminates timeouts entirely. Requires a Google AI Studio API key (see Issue 6 for rate limit details).

---

### Issue 5: Sandbox State Lost After System Restart

**Symptom:**
```
[resume] Recorded sandbox state is unavailable; recreating it.
Creating sandbox 'nemomsi' (this takes a few minutes on first run)...
```

**What's happening:** NemoClaw's sandbox is a Docker container — Docker containers do not automatically restart when your PC reboots (unless configured to). When the container stops, its runtime state is lost. The Docker **image** is cached however, so recreation is faster than the original build.

**Recovery steps:**
```bash
# Just re-run the onboarding wizard — it detects cached state and skips completed steps
nemoclaw onboard
# Re-enter Brave Search API key when prompted (sandbox state was reset)
# Telegram config will be re-confirmed from saved credentials
```

**To prevent this — configure Docker auto-restart:**
```bash
docker update --restart=unless-stopped openshell-cluster
```

This tells Docker Desktop to restart the gateway container automatically when Windows starts. The sandbox still needs `nemoclaw onboard` resume to reconnect, but the gateway will be available immediately.

---

### Issue 6: Google Gemini Free Tier Rate Limiting

**Full error (Telegram):**
```
⚠ All models are temporarily rate-limited. Please try again in a few minutes.
⚠ API rate limit reached. Please try again later.
```

**What's happening:** The Google Gemini API free tier (AI Studio) limits approximately 15 requests per minute and 1,500 per day. An active Telegram bot burns through these quickly — especially since each tool call the agent makes is an additional API request on top of the response itself.

**Fix — Upgrade to paid access:**

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create a new project (e.g., `Nemoclaw-bot`)
3. **Billing** → link a payment method
4. **APIs & Services** → **Library** → search **Gemini API** → Enable
5. **APIs & Services** → **Credentials** → **Create API Key** → copy it
6. Re-run `nemoclaw onboard` → select Google Gemini → paste the new key

**If using a school/org Google account (.edu email):**

You may see: `API Keys are Disallowed — Your organization's security policy disallows API keys.`

**Fix:** Create a separate personal Gmail account at `gmail.com` and use that for Google AI Studio and Google Cloud. Personal accounts have full API key creation rights.

> 💡 Cost context: Gemini 2.5 Flash on the paid tier typically costs less than $1/month for light to moderate personal use.

---

### Issue 7: Agent Ignores SOUL.md / Responds with Wrong Name

**Symptom:** You ask *"what is your name?"* and the agent replies *"I'm an AI assistant"* or *"You can call me Assistent"* — ignoring what you set in SOUL.md or IDENTITY.md.

**Cause A — File is in the wrong location:**

The workspace path must be exactly `/sandbox/.openclaw/workspace/SOUL.md`. Files created outside this directory will never be read.

**Cause B — File not saved through the dashboard editor:**

If you typed content in the OpenClaw dashboard editor but didn't click **Save**, the file exists in the UI but was never written to disk.

**Fix:**
1. In the OpenClaw dashboard → **Agents** → **Files**
2. Click **SOUL** (or **IDENTITY**) in the Core Files section
3. Verify the content is correct in the editor
4. Click the red **Save** button (top right of the editor)
5. Start a `/new` session in Telegram to clear the old context
6. Ask *"what is your name?"* again

> 💡 Always use `/new` in Telegram after making workspace file changes — the agent won't pick up new identity files mid-session.

---

### Issue 8: Port Conflict on Onboarding Resume

**Symptom:**
```
! Port 18789 is taken. Using port 18790 instead.
```

**What's happening:** A previous NemoClaw gateway is still running on port 18789, and the new onboarding run is trying to start a second one on the same port. NemoClaw just picks the next available port rather than killing the existing process.

**Fix Option 1 — Use the new port:** Update your bookmarked dashboard URL from `18789` to `18790`. The new tokenized URL will be printed in the terminal.

**Fix Option 2 — Kill the old gateway first:**
```bash
# Find and stop the container using port 18789
docker ps | grep 8789
docker stop <container-id>

# Then re-run onboarding
nemoclaw onboard
```

**Prevention:**
```bash
# Before onboarding, check if a gateway is already running
docker ps | grep openshell

# If running and healthy, skip onboarding entirely and just reconnect:
nemoclaw nemomsi connect
```

---

### Troubleshooting Quick Reference

| # | Issue | Root Cause | Fix |
|---|---|---|---|
| 1 | Docker Desktop ownership error | Installer run without admin rights | Run installer as Administrator |
| 2 | Gateway token mismatch | Navigated to dashboard without token fragment | Use full tokenized URL from terminal |
| 3 | Telegram API blocked by sandbox | Node.js binary not in OPA allowlist | Edit `policy.yaml`, add binary paths, reapply |
| 4 | Inference timeouts (Ollama) | 7B model too slow on 6 GB VRAM | Increase timeout, use smaller model, or use Gemini |
| 5 | Sandbox lost on restart | Docker containers don't persist reboots | Use resume mode; set Docker restart policy |
| 6 | Gemini rate limiting | Free tier API limits exceeded | Enable Google Cloud billing + paid API key |
| 7 | Agent ignores identity files | Wrong file path or file not saved | Verify path, click Save in dashboard, use `/new` |
| 8 | Port conflict on resume | Previous gateway still on 18789 | Update URL to new port or kill old container |

---

## 🤖 Live Agent Interaction

### Telegram (`@KinsroseBot`)

After all fixes were applied, Kinsey's first fully successful autonomous response via Telegram (using Gemini 2.5 Flash):

> *"I'm currently active and running with the inference/gemini-2.5-flash model. My execution is direct, and I'm using the OpenClaw Pi Default runtime. How can I help you today?"*

Tool calls were visible in the message stream, showing the agent's internal reasoning process:
```
Working...
• session_status: default
• tool: session_status
• sessions_send: {}
```

When asked *"what are your capabilities?"*:

> *"I can read and write files, execute shell commands, manage background processes, search the web, fetch web content, control a web browser, manage cron jobs for reminders and scheduled tasks, and interact with paired nodes for camera, screen, and device control. I can also manage other OpenClaw sessions and sub-agents, and send messages with rich content."*

### OpenClaw Web Dashboard (`127.0.0.1:18789`)

The dashboard provided full agent management including:
- Real-time chat with model selector (`inference/gemini-2.5-flash`)
- Agent file editor for all workspace files
- Channels, Instances, Sessions, Usage, and Cron Jobs panels
- OpenClaw v2026.4.29 — green connected status

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
│  │  Ollama      │    │  └──────────┬────────────────┘   │  │
│  └──────────────┘    │             │                      │  │
│                      │  ┌──────────▼────────────────┐   │  │
│                      │  │   NemoClaw Sandbox          │   │  │
│                      │  │   Agent: Kinsey             │   │  │
│                      │  │                             │   │  │
│                      │  │   Workspace Files:          │   │  │
│                      │  │   ├── SOUL.md               │   │  │
│                      │  │   ├── IDENTITY.md           │   │  │
│                      │  │   ├── AGENTS.md             │   │  │
│                      │  │   ├── USER.md               │   │  │
│                      │  │   ├── MEMORY.md             │   │  │
│                      │  │   └── HEARTBEAT.md          │   │  │
│                      │  │                             │   │  │
│                      │  │   OPA Network Policy        │   │  │
│                      │  └───────────────────────────┘   │  │
│                      └─────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
           │                   │                  │
           ▼                   ▼                  ▼
    ┌────────────┐   ┌────────────────┐   ┌────────────┐
    │  Telegram  │   │ Google Gemini  │   │   Brave    │
    │ @KinsroseBot│  │ 2.5 Flash API  │   │ Search API │
    └────────────┘   └────────────────┘   └────────────┘
```

---

## 🔑 Skills Demonstrated

| Skill Area | Details |
|---|---|
| **Agentic AI Architecture** | Deployed a production-grade multi-component AI agent from scratch on consumer hardware |
| **Agent Persona Design** | Authored SOUL.md, IDENTITY.md, USER.md, MEMORY.md, and AGENTS.md to create a persistent, personalized agent identity |
| **Linux / WSL2** | Multi-distro WSL2, YAML editing in `nano`, process management, `ollama` and `openshell` CLI usage |
| **Docker / Containerization** | Resolved install errors, managed containers, understood 56-step multi-stage Dockerfile builds |
| **Network Security & Policy** | Manually modified OPA-based network policy YAML; configured binary allowlists for external endpoints |
| **API Integration** | Brave Search API, Telegram Bot API (BotFather), Google Gemini API |
| **Google Cloud Platform** | Set up GCP projects, enabled APIs, configured billing, navigated IAM and Agent Platform Studio |
| **Debugging & Problem Solving** | Independently resolved 8 distinct technical issues across Docker, WSL, APIs, YAML policy, and auth |
| **LLM Model Management** | Downloaded and benchmarked multiple Ollama models; understood quantization formats; switched local↔cloud inference |
| **Git / GitHub** | Created and published a public documentation repository |

---

## 📦 Ollama Models Tested

| Model | Size | Notes |
|---|---|---|
| `qwen2.5:7b` | 4.7 GB | Initial model — 100% GPU, slow responses |
| `qwen2.5:3b` | ~2 GB | Faster for local development testing |
| `gemma4:latest` | 9.6 GB | Downloaded for comparison |
| `qwen3.5:9b-q4_K_M` | 6.6 GB | Quantized model for performance testing |
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
