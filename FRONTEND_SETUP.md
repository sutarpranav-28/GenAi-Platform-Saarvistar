# Saarvistar — Frontend Setup & Launch Guide

Complete step-by-step guide for installing, configuring, running, and deploying the **Saarvistar Enterprise GenAI Content Transformation Platform**.

---

## 📑 Table of Contents

1. [Prerequisites & System Requirements](#1-prerequisites--system-requirements)
2. [Project Setup & Installation](#2-project-setup--installation)
3. [Environment Configuration (`.env.local`)](#3-environment-configuration-envlocal)
4. [Launching the Web Platform (Development)](#4-launching-the-web-platform-development)
5. [Production Build & Verification](#5-production-build--verification)
6. [Interactive Walkthrough & Feature Verification](#6-interactive-walkthrough--feature-verification)
7. [Project Directory & Architecture Overview](#7-project-directory--architecture-overview)
8. [Troubleshooting & Common FAQs](#8-troubleshooting--common-faqs)

---

## 1. Prerequisites & System Requirements

Before setting up Saarvistar, ensure your development workstation meets the following requirements:

| Component | Minimum Version | Recommended |
| :--- | :--- | :--- |
| **Node.js** | `v18.18.0+` | `v20.x LTS` or higher |
| **Package Manager** | `npm v9+` | `npm` (or `pnpm v8+`) |
| **Operating System** | Windows 10/11, macOS, Linux | Any modern OS |
| **Web Browser** | Any modern evergreen browser | Google Chrome, Microsoft Edge, Brave, Firefox |

### Verify Node & npm Versions
Run the following commands in your terminal (PowerShell, Command Prompt, or Bash):

```bash
node -v
# Output should be >= v18.18.0 (e.g. v20.12.0)

npm -v
# Output should be >= 9.0.0
```

> **Note for Windows Users**: PowerShell or Windows Terminal is recommended. Ensure your PowerShell execution policy allows script execution:
> ```powershell
> Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned
> ```

---

## 2. Project Setup & Installation

### Step 2.1: Navigate to the Project Root
Open your terminal and navigate to the Saarvistar project directory:

```bash
cd "c:\Users\Aarya Raut\OneDrive\Desktop\TECHTITANSXX\Saarvistar"
```

### Step 2.2: Install Dependencies
Install all required runtime and development dependencies:

```bash
npm install
```

*(Alternative using pnpm if installed: `pnpm install`)*

If you encounter any dependency peer resolution conflicts on legacy node environments, run:
```bash
npm install --legacy-peer-deps
```

### Key Libraries Installed
- **Framework**: `Next.js 14 (App Router)` with `React 18`
- **State Management**: `zustand` (with local storage persistence)
- **Styling**: `Tailwind CSS 3`, `PostCSS`, `Autoprefixer`, `tailwind-merge`, `clsx`
- **Iconography & UI**: `lucide-react`, `@radix-ui/react-*`, `framer-motion`
- **Validation & AI SDKs**: `zod`, `react-hook-form`, `@anthropic-ai/sdk`

---

## 3. Environment Configuration (`.env.local`)

Saarvistar operates in two modes:
1. **Deterministic Demo Mode (Default)**: Runs without requiring any external backend, API keys, or live n8n webhooks. Pre-populated with cybersecurity incident simulations for all 8 formats.
2. **Live Production Mode**: Connects directly to your live **n8n workflow automation webhook** and optional LLM endpoints.

### Step 3.1: Create `.env.local`
In the project root, create a file named `.env.local` (copied from `.env.local.example`):

**On Windows (PowerShell):**
```powershell
Copy-Item .env.local.example .env.local
```

**On macOS/Linux:**
```bash
cp .env.local.example .env.local
```

### Step 3.2: Configure Environment Variables

Open `.env.local` in your editor and configure the variables:

```env
# ─────────────────────────────────────────────────────────────────────────────
# Saarvistar Environment Configuration
# ─────────────────────────────────────────────────────────────────────────────

# MODE SELECTION:
# true  = Deterministic Demo Mode (zero latency, zero webhook required)
# false = Live production mode (forwards requests to N8N_WEBHOOK_URL)
NEXT_PUBLIC_DEMO_MODE=true

# REQUIRED IF DEMO_MODE IS FALSE:
# The full URL of your n8n workflow webhook
# Example: https://n8n.yourdomain.com/webhook/saarvistar-transform
N8N_WEBHOOK_URL=https://your-n8n-instance.example.com/webhook/abc123

# OPTIONAL:
# Shared secret sent in the "X-Webhook-Secret" header to authenticate with n8n
N8N_WEBHOOK_SECRET=your_optional_webhook_secret_key

# OPTIONAL:
# Anthropic Claude API key used by streaming LLM endpoints
ANTHROPIC_API_KEY=
```

> 💡 **Recommendation for First Run**: Leave `NEXT_PUBLIC_DEMO_MODE=true` for instant, full-fidelity preview of all UI cards, formats, inline editing, and exports without external dependencies.

---

## 4. Launching the Web Platform (Development)

### Step 4.1: Start the Development Server
Execute:

```bash
npm run dev
```

You should see output similar to:
```
  ▲ Next.js 14.2.18
  - Local:        http://localhost:3000
  - Environments: .env.local

 ✓ Starting...
 ✓ Ready in 1.8s
```

### Step 4.2: Open in Browser
Open your browser and navigate to:
👉 **[http://localhost:3000](http://localhost:3000)**

### Default Port Conflicts
If port `3000` is already in use by another process, start on a custom port:
```bash
npm run dev -- -p 3001
```
Then access **http://localhost:3001**.

---

## 5. Production Build & Verification

Before deploying to production, verify that TypeScript compilation and the production bundle build succeed with zero errors.

### Step 5.1: Run TypeScript Typecheck
```bash
npx tsc --noEmit
```
*Expected result: Exits cleanly with code 0 and no type errors.*

### Step 5.2: Build the Production Bundle
```bash
npm run build
```
This compiles and optimizes all server components, client components, and static assets into the `.next` directory.

### Step 5.3: Run the Production Server Locally
```bash
npm run start
```
Starts the optimized production server at `http://localhost:3000`.

---

## 6. Interactive Walkthrough & Feature Verification

Once the platform is running at `http://localhost:3000`, test the following core features:

### 1. Home Composer (`/`)
- Paste text into the incident text box or attach a PDF/document using the paperclip button.
- Choose which formats you want to generate using the **Target Formats** multi-select dropdown.
- Click **Transform Content** or use a sample preset chip (e.g. *"Fictional M365 Credential Compromise"*).

### 2. Transformation Workspace (`/chat/[chatId]`)
- **Vertical Format Switcher (Left Dock)**: Click through the format icons:
  - 🛡️ **Public Advisory** (`structured_advisory`) — Priority sections, TL;DR, key findings, and action items matrix.
  - 📖 **Executive Summary** (`exec_summary`) — High-level business takeaways and tone indicators.
  - 🎥 **Video Package** (`video`) — Multi-scene production script with voiceover and visual cues.
  - 📊 **Infographic** (`infographic`) — Stat cards with trend indicators and vertical timeline.
  - 📽️ **Presentation Deck** (`presentation`) — Interactive slide carousel, slide previews, and speaker notes.
  - 💼 **LinkedIn Post** (`linkedin_post`) — Formatted thought leadership post with hashtags and character counters.
  - 🏷️ **Twitter/X Thread** (`twitter_thread`) — Numbered micro-posts (`1/N`) with tweet cards.

### 3. Inline Editing
- Click the **Edit** button in the header of any format card.
- Directly modify headings, bullet points, table cells, or scene narration.
- Click **Done** — changes are instantly saved to the global Zustand store and persisted locally.

### 4. Smart Copy & Verification
- Click **Copy** on any format to test the smart format-aware clipboard text generator.
- Click **Verify** on any format card to experience the multi-state verification lifecycle (`Verify` ➔ `Verifying…` ➔ `Verified ✓` ➔ `Upload`).

### 5. Presentation PDF & Canva Export
- Select the **Presentation** tab.
- Click **Download** to download `Saarvistar_Presentation.pdf`.
- Click **Export to Canva** to trigger the PDF download and automatically launch the Canva upload interface (`https://www.canva.com/upload`) in a new tab.

### 6. Dynamic Refinements via Prompt Dock
- Type a directive into the bottom prompt dock (e.g., *"Make the tone more direct"*).
- The prompt dock automatically submits a contextual refinement request targeting the currently active format tab.

### 7. Customization & Multi-Language
- Click the user profile icon at the bottom of the sidebar.
- Switch languages between **English**, **हिन्दी (Hindi)**, and **मराठी (Marathi)**.
- Open **Preferences** to toggle between **Dark**, **Light**, or **System** mode and **Comfortable** vs **Compact** display density.

---

## 7. Project Directory & Architecture Overview

```
Saarvistar/
├── app/
│   ├── api/transform/route.ts       # Backend transformation route & n8n proxy
│   ├── chat/[chatId]/page.tsx       # Main transformation workspace page
│   ├── layout.tsx                   # Root HTML shell, fonts, and theme sync
│   └── page.tsx                     # Home landing page with EmptyState composer
│
├── components/
│   ├── chat/
│   │   ├── formats/                 # Specialized format renderers
│   │   │   ├── ExecutiveSummary.tsx
│   │   │   ├── Infographic.tsx
│   │   │   ├── LinkedInPost.tsx
│   │   │   ├── Presentation.tsx
│   │   │   ├── PublicAdvisory.tsx
│   │   │   ├── StructuredAdvisory.tsx
│   │   │   ├── TwitterThread.tsx
│   │   │   └── VideoPackage.tsx
│   │   ├── ContentCard.tsx          # Card shell (Edit, Verify, Copy, Canva)
│   │   ├── EmptyState.tsx           # Multi-mode input composer
│   │   ├── FormatTabs.tsx           # Vertical format switcher dock
│   │   ├── OutputPanel.tsx          # Format orchestrator
│   │   └── PromptDock.tsx           # Floating contextual AI prompt dock
│   │
│   ├── common/
│   │   ├── CopyButton.tsx           # Formatted clipboard button with toast
│   │   ├── SarvistarMark.tsx        # Brand SVG logo mark
│   │   ├── ThemeSync.tsx            # Theme listener & class injector
│   │   └── WorkspacePreferencesModal.tsx # Preferences modal (Theme, Density, Language)
│   │
│   └── layout/
│       ├── Sidebar.tsx              # Collapsible sidebar with projects & history
│       └── TopHeader.tsx            # Glassmorphic top bar with toast capsule
│
├── lib/
│   ├── demoData.ts                  # Deterministic simulation fixtures
│   ├── n8nParser.ts                 # Response parser & format normalizer
│   ├── store.ts                     # Zustand persistent state store
│   └── types.ts                     # TypeScript definitions for all formats
│
├── public/                          # Static assets (PDFs, icons, previews)
├── styles/globals.css               # Design tokens, fonts, and Tailwind styles
├── LAYOUTS_AND_FEATURES.md          # Full architectural layout & feature reference
├── FRONTEND_SETUP.md                # This setup guide
└── package.json                     # Scripts & project dependencies
```

---

## 8. Troubleshooting & Common FAQs

### Q1: `Error: listen EADDRINUSE: address already in use :::3000`
**Solution**: Another application is using port 3000. Either specify a different port or terminate the existing process:
```bash
# Option A: Start on an alternative port
npm run dev -- -p 3005

# Option B (Windows PowerShell): Find and kill the process on port 3000
Get-Process -Id (Get-NetTCPConnection -LocalPort 3000).OwningProcess | Stop-Process -Force
```

### Q2: Stale cache or compilation errors after switching branches
**Solution**: Clear the Next.js build cache and re-run:
```powershell
# PowerShell
Remove-Item -Recurse -Force .next
npm run dev
```
```bash
# Bash
rm -rf .next
npm run dev
```

### Q3: `Cannot find name 'deleteProject'` or store compilation issue
**Solution**: Ensure [Sidebar.tsx](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/layout/Sidebar.tsx#L48-L55) imports all project methods from `useAppStore()`. Run `npx tsc --noEmit` to verify type safety.

### Q4: Transformations return 503 or "Server configuration error"
**Solution**: If you set `NEXT_PUBLIC_DEMO_MODE=false` in `.env.local`, you must supply a reachable `N8N_WEBHOOK_URL`. If you do not have an active n8n instance yet, set `NEXT_PUBLIC_DEMO_MODE=true` in `.env.local` to use the deterministic offline test fixtures.

### Q5: How do I reset stored sessions or restore demo data?
**Solution**: Saarvistar persists session state in browser `localStorage` under the key `saarvistar-sessions-v3`. To clear all saved sessions:
1. Open Developer Tools (`F12`) in your browser.
2. Go to **Application** ➔ **Local Storage** ➔ `http://localhost:3000`.
3. Right-click and choose **Clear**, then refresh the page (`F5`).

---

## 🚀 Quick Launch Command Summary

```bash
# 1. Install dependencies
npm install

# 2. Setup environment file
copy .env.local.example .env.local

# 3. Start development server
npm run dev

# 4. Open in browser
# Navigate to http://localhost:3000
```
