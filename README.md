# Saarvistar: GenAI Content Transformation Platform

> **Smart India Hackathon 2026** | Problem Statement **SIH26154**: Gen AI Platform for Automated Content Transformation
> Theme: Blockchain & Cybersecurity | Category: Software | Team: **Tech Titans** (Team ID: SW-1-01)

Saarvistar turns **one source** (a PDF report, DOCX, article, or free text) into **many audience-specific deliverables**: Public Advisory, Executive Summary, LinkedIn Post, X (Twitter) Thread, Video Package, Infographic and Presentation Deck. It runs on local models through Ollama, so no sensitive content leaves your machine.

**Key approach:** One source → Multiple outputs → Verified content

---

## Table of Contents

1. [Features](#features)
2. [Architecture](#architecture)
3. [Tech Stack](#tech-stack)
4. [Project Structure](#project-structure)
5. [Prerequisites](#prerequisites)
6. [Setup](#setup)
   - [1. LLM setup (Ollama, Phi-3, GLM-OCR)](#1-llm-setup-ollama-phi-3-glm-ocr)
   - [2. n8n backend setup](#2-n8n-backend-setup)
   - [3. Frontend setup](#3-frontend-setup)
7. [Environment Variables](#environment-variables)
8. [Running the Full System](#running-the-full-system)
9. [Using the Platform](#using-the-platform)
10. [Production Build](#production-build)
11. [Troubleshooting](#troubleshooting)
12. [Notes](#notes)
13. [Team](#team)

---

## Features

- **Multi-mode input:** paste text, upload PDF / DOCX / TXT / images, or combine a source file with your own instructions (for example, *"Focus on remediation timelines and omit IP addresses"*).
- **Seven output formats** from a single source:

  | Format | Audience | Focus |
  | :--- | :--- | :--- |
  | Public Advisory | CISO, SecOps, public stakeholders | Severity tags, key findings, recommendations, action-items table |
  | Executive Summary | C-suite, board | Business impact, strategic takeaways, risk |
  | LinkedIn Post | Industry peers | Thought-leadership post with hashtags and live character count |
  | X (Twitter) Thread | Public feed | Numbered micro-posts (`1/N`) with a 280-character meter |
  | Video Package | Internal comms, training | Scene-by-scene script with timing, voiceover, and visual cues |
  | Infographic | Dashboards, social | KPI cards, trend arrows, incident timeline |
  | Presentation Deck | Board meetings | Slide carousel, bullet hierarchy, speaker notes, PDF export |

- **Fact verification:** a verification agent checks generated claims against the source, and each output moves through a human-approval flow (`Verify` → `Verifying…` → `Verified ✓` → `Upload`).
- **Universal inline editing:** edit any heading, bullet, table cell, or narration, and changes persist locally.
- **Format-aware copy:** the clipboard output follows each format's conventions (Markdown, transcripts, `1/N` tweet breaks).
- **Prompt dock:** ask for refinements in plain language ("make the tone more urgent"); only the active format regenerates.
- **Presentation export:** download a PDF, or export to Canva.
- **Workspace features:** session history, pinning, renaming, project folders, dark / light / system themes, comfortable / compact density, and English / हिन्दी / मराठी interface.
- **Two run modes:** an offline **Demo Mode** with realistic sample data, and a **Live Mode** backed by n8n and local LLMs.

---

## Architecture

```
┌──────────────────────┐   request (source + selected formats)   ┌────────────────────────────────────────┐
│  Frontend (Next.js)  │ ──────────────────────────────────────► │  n8n workflow (backend)                │
│                      │                                          │  1. Text extract & normalize (GLM-OCR) │
│  Composer, workspace,│                                          │  2. Template resolver                  │
│  editor, exports     │                                          │  3. AI generation agents (parallel)    │
│                      │ ◄────────────────────────────────────── │     LinkedIn / X / Advisory / Summary  │
└──────────────────────┘   webhook response (outputs + status)    │     Infographic / Presentation / Video │
                                                                  │  4. Fact verification agent            │
                                                                  │  5. Compile outputs                    │
                                                                  └───────────────────┬────────────────────┘
                                                                                      │
                                                                             Ollama (Phi-3, GLM-OCR)
                                                                             running at localhost:11434
```

In Live Mode, the frontend's `/api/transform` route forwards multipart form data or JSON to the n8n webhook with a 120-second timeout and an optional `X-Webhook-Secret` header. A parser (`lib/n8nParser.ts`) normalizes the returned formats.

---

## Tech Stack

| Layer | Technology |
| :--- | :--- |
| Frontend | Next.js 14 (App Router), React 18, TypeScript, Tailwind CSS, Zustand, Framer Motion, Radix UI, lucide-react |
| Automation / backend | n8n (self-hosted) |
| LLMs | Ollama with **Phi-3** (content transformation) and **GLM-OCR** (PDF text extraction) |
| Data | Supabase |
| Hosting (target) | Oracle Cloud VPS (VM.Standard.A1.Flex) |

---

## Project Structure

```
├── frontend/              # Next.js dashboard app (Saarvistar)
│   ├── app/               # Routes: home composer, /chat/[chatId], /api/transform
│   ├── components/        # chat/, common/, layout/ (format renderers, sidebar, header)
│   ├── lib/               # store.ts, n8nParser.ts, demoData.ts, types.ts
│   ├── public/            # static assets and sample PDFs
│   └── styles/            # Tailwind tokens and global CSS
├── n8n-workflow/          # Exported n8n workflow JSON (backend orchestration)
├── docs/                  # Architecture doc, diagrams, presentation
└── README.md
```

---

## Prerequisites

- [Node.js](https://nodejs.org/) **v18.18+** (v20 LTS recommended) and npm v9+
- [n8n](https://n8n.io/) (installed locally via npm, see below)
- [Ollama](https://ollama.com/) (installed locally, see below)
- Git
- At least **8 GB of free RAM** to run Phi-3 and GLM-OCR locally
- A modern browser (Chrome, Edge, Brave, or Firefox)

Verify your versions:

```bash
node -v   # >= v18.18.0
npm -v    # >= 9.0.0
```

> **Windows users:** use PowerShell or Windows Terminal, and make sure scripts are allowed:
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
> ```

> **Just want to see the UI?** Skip to [3. Frontend setup](#3-frontend-setup). Demo Mode works without Ollama or n8n.

---

## Setup

Set up the three parts in this order: **LLM (Ollama)** → **n8n backend** → **frontend**.

First, clone the repository:

```bash
git clone <your-repo-url>
cd <repo-folder>
```

### 1. LLM setup (Ollama, Phi-3, GLM-OCR)

All generation runs locally through Ollama, so no external API key is required. Phi-3 handles content transformation, and GLM-OCR handles text extraction from uploaded PDFs.

**Step 1: Install Ollama**

macOS / Linux:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Windows: download and run the installer from [ollama.com/download](https://ollama.com/download).

```bash
ollama --version
```

**Step 2: Start the Ollama service**

```bash
ollama serve
```

This serves Ollama at `http://localhost:11434`. Keep it running, because n8n calls this endpoint.

**Step 3: Pull Phi-3**

In a new terminal:

```bash
ollama pull phi3
ollama run phi3 "Summarize this in one sentence: n8n orchestrates the workflow."
```

**Step 4: Pull GLM-OCR**

```bash
ollama pull glm-ocr
```

> If `glm-ocr` isn't available under that name in your Ollama version, search `ollama list` or [ollama.com/library](https://ollama.com/library) for the GLM OCR-capable tag your team uses (for example, a GLM-4V variant), and use that tag instead.

**Step 5: Confirm both models are available**

```bash
ollama list
```

You should see `phi3` and `glm-ocr` (or your confirmed OCR tag).

### 2. n8n backend setup

**Step 1: Install n8n**

```bash
npm install n8n -g
```

**Step 2: Start n8n**

```bash
n8n start
```

n8n opens at `http://localhost:5678`.

**Step 3: Create a local account**

On first run, n8n asks you to create a local owner account. This is stored locally and is not a cloud account.

**Step 4: Import the workflow**

1. In the n8n editor, open the menu (⋮) in the top right → **Import from File**.
2. Select the workflow JSON from `n8n-workflow/` (for example, `content-transformer-workflow.json`).
3. The full workflow (Webhook → text extraction → agents → verification → response) appears on the canvas.

**Step 5: Connect n8n to Ollama**

In each AI Agent / Chat Model node:

1. Add a new **Ollama** credential.
2. Set the **Base URL** to `http://localhost:11434`.
3. Select `phi3` for the transformation agents, and your GLM-OCR tag for the PDF extraction step.

`ollama serve` must be running before you test any node.

**Step 6: Activate the workflow**

Toggle the workflow to **Active** (top right). This gives the webhook a stable, callable URL.

**Step 7: Copy the webhook URL**

Open the **Webhook** trigger node and copy the **Production URL**. It is only available once the workflow is Active. You will paste it into the frontend's `.env.local`.

### 3. Frontend setup

**Step 1: Install dependencies**

```bash
cd frontend
npm install
```

If you hit peer-dependency conflicts:

```bash
npm install --legacy-peer-deps
```

**Step 2: Create your environment file**

Windows (PowerShell):

```powershell
Copy-Item .env.local.example .env.local
```

macOS / Linux:

```bash
cp .env.local.example .env.local
```

**Step 3: Configure `.env.local`**

For a first run, keep Demo Mode on:

```env
# true  = Demo Mode (offline sample data, no backend needed)
# false = Live Mode (forwards requests to N8N_WEBHOOK_URL)
NEXT_PUBLIC_DEMO_MODE=true

# Required when NEXT_PUBLIC_DEMO_MODE=false
N8N_WEBHOOK_URL=https://your-n8n-instance.example.com/webhook/abc123

# Optional: shared secret sent as the X-Webhook-Secret header
N8N_WEBHOOK_SECRET=your_optional_webhook_secret_key

# Optional: only if you use a streaming LLM endpoint
ANTHROPIC_API_KEY=
```

To connect to your local n8n, set `NEXT_PUBLIC_DEMO_MODE=false` and paste the **Production URL** from n8n step 7 into `N8N_WEBHOOK_URL`.

**Step 4: Start the dev server**

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000). If port 3000 is busy:

```bash
npm run dev -- -p 3001
```

---

## Environment Variables

| Variable | Description | Where used |
| :--- | :--- | :--- |
| `NEXT_PUBLIC_DEMO_MODE` | `true` for offline sample data, `false` for live n8n | Frontend `.env.local` |
| `N8N_WEBHOOK_URL` | Production webhook URL from the n8n workflow | Frontend `.env.local` (Live Mode) |
| `N8N_WEBHOOK_SECRET` | Optional shared secret sent as `X-Webhook-Secret` | Frontend `.env.local` |
| `ANTHROPIC_API_KEY` | Optional key for streaming LLM endpoints | Frontend `.env.local` |
| Ollama credentials | Base URL `http://localhost:11434`, set inside n8n node credentials | n8n only |

---

## Running the Full System

1. Start Ollama (`ollama serve`) and confirm `phi3` and your GLM-OCR model appear in `ollama list`.
2. Start n8n (`n8n start`) and confirm the workflow is **Active**.
3. Set `NEXT_PUBLIC_DEMO_MODE=false` and your `N8N_WEBHOOK_URL` in `frontend/.env.local`.
4. Start the frontend (`npm run dev` inside `frontend/`).
5. Open the dashboard, submit a PDF or text, and select one or more output formats.
6. The frontend calls the n8n webhook → n8n runs GLM-OCR (if a PDF was uploaded) → the agents call Phi-3 for each selected format → the verification agent checks the results → the outputs return to the dashboard.

---

## Using the Platform

**Home composer (`/`)**
Paste text or attach a PDF/document, choose formats in the **Target Formats** dropdown, and click **Transform Content**. You can also click a sample preset such as *"Fictional M365 Credential Compromise"*.

**Transformation workspace (`/chat/[chatId]`)**
Use the vertical dock on the left to switch between formats. Each format has its own layout, an **Edit / Done** toggle, **Copy**, and **Verify**.

**Verification lifecycle**
`Verify` → `Verifying…` → `Verified ✓` → `Upload`

**Presentation export**
On the Presentation tab, click **Download** to get `Saarvistar_Presentation.pdf`, or **Export to Canva** to download the PDF and open Canva's upload page in a new tab.

**Prompt dock**
Type a directive (for example, *"Shorten the video script to 60 seconds"*) at the bottom. Only the active format regenerates.

**Customization**
Open the profile menu in the sidebar to switch language (English, Hindi, Marathi), theme (Dark, Light, System), and density (Comfortable, Compact).

---

## Production Build

```bash
cd frontend
npx tsc --noEmit     # type check, should exit with no errors
npm run build        # compile the production bundle
npm run start        # serve at http://localhost:3000
```

---

## Troubleshooting

**`EADDRINUSE: address already in use :::3000`**
Start on another port with `npm run dev -- -p 3005`, or on Windows PowerShell stop the process:
```powershell
Get-Process -Id (Get-NetTCPConnection -LocalPort 3000).OwningProcess | Stop-Process -Force
```

**Stale cache or compile errors after switching branches**
```bash
rm -rf .next && npm run dev                 # macOS / Linux
Remove-Item -Recurse -Force .next; npm run dev   # PowerShell
```

**Transformations return 503 or "Server configuration error"**
You have `NEXT_PUBLIC_DEMO_MODE=false` without a reachable `N8N_WEBHOOK_URL`. Either supply a live webhook URL or set `NEXT_PUBLIC_DEMO_MODE=true`.

**n8n nodes fail to connect to the model**
Make sure `ollama serve` is running, the credential Base URL is `http://localhost:11434`, and the model names in the nodes match `ollama list`.

**Webhook URL doesn't work**
Use the **Production URL** (not the Test URL) and make sure the workflow is toggled **Active**.

**Type error such as `Cannot find name 'deleteProject'`**
Make sure `components/layout/Sidebar.tsx` imports all project methods from `useAppStore()`, then run `npx tsc --noEmit`.

**Reset saved sessions**
Sessions are stored in browser `localStorage` under `saarvistar-sessions-v3`. Open DevTools (`F12`) → **Application** → **Local Storage** → `http://localhost:3000` → **Clear**, then refresh.

---

## Notes

- n8n and Ollama must both stay running for the webhook to respond. For a persistent team demo, consider running them with [Docker](https://docs.n8n.io/hosting/installation/docker/) or on a small cloud VM.
- Running Phi-3 and GLM-OCR locally means no per-request API cost and no sensitive content leaving your machine, a deliberate choice for a security and intelligence-focused platform.
- Do **not** commit `.env` or `.env.local` files or API keys. Add them to `.gitignore`.
- The sample incident data in Demo Mode is synthetic and created for demonstration only.

---

## Team

| Name | Role |
| :--- | :--- |
| Pranav Sutar (Team Leader) | Automation workflow and agents in n8n |
| Kaiwalya Raut | Frontend |
| Shrungeri Deshpande | Frontend |
| Rasika Patil | Backend |
| Jyoti Singh | Integrations |

---

© Tech Titans, Pranav Sutar. Built for Smart India Hackathon 2026.
