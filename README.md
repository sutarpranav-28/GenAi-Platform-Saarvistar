# GenAI Content Transformation Platform (SIH Project)

An AI-powered platform that transforms source content (PDF reports, articles,
free-text prompts) into audience-specific deliverables — Executive Summary,
LinkedIn Post, Instagram Post, and more — through a configurable dashboard.

## Project structure

```
├── frontend/           # Windows dashboard app
├── n8n-workflow/        # Exported n8n workflow JSON (backend orchestration)
├── docs/                 # Architecture doc, diagrams, presentation
└── README.md
```

## Prerequisites

- [Node.js](https://nodejs.org/) v18+ and npm
- [n8n](https://n8n.io/) (installed locally via npm — steps below)
- [Ollama](https://ollama.com/) (installed locally — steps below)
- Git
- At least 8GB free RAM (for running Phi-3 and GLM-OCR locally)

## Setup

There are three parts to set up, in order: **LLM setup (Ollama + models)**,
the **n8n backend**, and the **frontend dashboard app** (setup instructions
added separately once finalized).

### 1. LLM setup (Ollama + Phi-3 + GLM-OCR)

This project runs its models locally via Ollama — no external API key is
required for generation. Phi-3 handles content transformation (Executive
Summary, LinkedIn, Instagram generation) and GLM-OCR handles text
extraction from uploaded PDFs.

**Step 1 — Install Ollama**

macOS / Linux:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Windows:

Download and run the installer from [ollama.com/download](https://ollama.com/download).

Verify the install:

```bash
ollama --version
```

**Step 2 — Start the Ollama service**

```bash
ollama serve
```

This runs Ollama locally at `http://localhost:11434`. Keep this running in
the background — n8n calls this endpoint to reach the models.

**Step 3 — Pull Phi-3**

In a new terminal:

```bash
ollama pull phi3
```

Test it:

```bash
ollama run phi3 "Summarize this in one sentence: n8n orchestrates the workflow."
```

**Step 4 — Pull GLM-OCR**

```bash
ollama pull glm-ocr
```

> If `glm-ocr` is not available directly under that name in your Ollama
> library version, search `ollama list` / [ollama.com/library](https://ollama.com/library)
> for the exact GLM OCR-capable model tag your team is using (e.g. a
> GLM-4V variant) and pull that tag instead — update this README with the
> exact name once confirmed.

Test it against a sample image or PDF page, per the model's usage docs.

**Step 5 — Confirm both models are available**

```bash
ollama list
```

You should see both `phi3` and `glm-ocr` (or your confirmed GLM OCR model
tag) listed.

### 2. n8n backend setup

**Step 1 — Install n8n locally**

```bash
npm install n8n -g
```

**Step 2 — Start n8n**

```bash
n8n start
```

This launches n8n at `http://localhost:5678`.

**Step 3 — Log in / create an account**

Open `http://localhost:5678` in your browser. On first run, n8n will prompt
you to create a local owner account (name, email, password) — this is
stored locally and is not a cloud account.

**Step 4 — Import the workflow**

1. In the n8n editor, click the **menu (⋮)** in the top right → **Import
   from File**.
2. Select the workflow JSON file from `n8n-workflow/` in this repo (e.g.
   `content-transformer-workflow.json`).
3. The full workflow (Merge → AI Agent → sub-agents → Webhook response)
   should now appear on the canvas.

**Step 5 — Connect n8n to Ollama**

This project uses n8n's built-in **Ollama** node/credential type instead of
a cloud LLM API key. In each AI Agent / Chat Model node that needs a model:

1. Add a new **Ollama** credential.
2. Set the **Base URL** to `http://localhost:11434` (the default Ollama
   address from step 1.2 above).
3. In the node's model field, select `phi3` for the transformation agents,
   and your GLM-OCR model tag for the PDF text-extraction step.

Make sure `ollama serve` is running (step 1.2) before testing any node —
if Ollama isn't running, the node will fail to connect.

**Step 6 — Activate the workflow**

Toggle the workflow to **Active** (top right of the editor). This is
required for the webhook to have a stable, callable URL.

**Step 7 — Get the webhook URL**

1. Open the **Webhook** trigger node at the start of the workflow.
2. Copy the **Production URL** shown in the node (not the Test URL — the
   production URL only becomes available once the workflow is Active).
3. This URL is what the frontend calls to submit content and receive
   generated output.

### 3. Frontend setup

_(To be added once the frontend build is finalized — see `frontend/README.md`)_

Once set up, create a `.env` file inside the `frontend/` directory with:

```
VITE_N8N_WEBHOOK_URL=<paste your production webhook URL here>
```

(Adjust the variable name/prefix to match your frontend framework's env
convention, e.g. `REACT_APP_` or `NEXT_PUBLIC_` if not using Vite.)

Then install and run the frontend as documented in `frontend/README.md`.

## Environment variables reference

| Variable | Description | Where used |
|---|---|---|
| `VITE_N8N_WEBHOOK_URL` | Production webhook URL from the n8n workflow | Frontend `.env` |
| (LLM API key) | Set inside n8n node credentials, not in `.env` | n8n only |

## Running the full system

1. Start Ollama (`ollama serve`) and confirm `phi3` and your GLM-OCR model
   are pulled (`ollama list`).
2. Start n8n (`n8n start`) and confirm the workflow is **Active**.
3. Start the frontend dev server.
4. Open the dashboard, submit a PDF/text + select output type(s).
5. The frontend calls the n8n webhook → n8n runs GLM-OCR (if a PDF was
   uploaded) → the orchestrator agent calls Phi-3 for the selected output
   type(s) → generated output is returned and displayed.

## Notes

- n8n and Ollama must both remain running for the webhook to respond. For
  a persistent team demo, consider running both via
  [Docker](https://docs.n8n.io/hosting/installation/docker/) or a small
  cloud VM instead of a local machine.
- Running Phi-3 and GLM-OCR locally means no per-request API cost and no
  sensitive content leaving your machine — worth noting in your
  architecture doc as a deliberate choice for a security/intelligence-
  context platform.
- Do not commit `.env` files or API keys to the repository — add `.env` to
  `.gitignore`.

## Team

| Name | Role |
|---|---|
| Pranav Sutar (Team Leader) | Automation Workflow and Agents in N8n |
|---|---|
| Kaiwalya Raut | Frontend |
|---|---|
| Shrungeri Deshpande | Frontend |
|---|---|
| Rasika Patil | Backend |
|---|---|
| Jyoti Singh | Integrations |

#Copy Right : 
Tech Titans --> Pranav Sutar.
