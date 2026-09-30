# Saarvistar — Layouts & Features Specification

Saarvistar is an enterprise **GenAI Content Transformation Platform** designed to convert complex technical documentation, operational incident reports, and raw security telemetry into synchronized, audience-specific intelligence channels.

---

## 📑 Table of Contents

1. [System Architecture & Design System](#1-system-architecture--design-system)
2. [Global Application Layouts](#2-global-application-layouts)
   - [2.1 Root Layout Shell](#21-root-layout-shell)
   - [2.2 Collapsible Sidebar Navigation](#22-collapsible-sidebar-navigation)
   - [2.3 Top Application Header](#23-top-application-header)
   - [2.4 Home Composer / Empty State Layout](#24-home-composer--empty-state-layout)
   - [2.5 Transformation Workspace Layout](#25-transformation-workspace-layout)
3. [Specialized Output Formats & Layout Blueprints](#3-specialized-output-formats--layout-blueprints)
   - [3.1 Structured Advisory Document](#31-structured-advisory-document-structured_advisory)
   - [3.2 Executive Summary](#32-executive-summary-exec_summary)
   - [3.3 Video Package Script](#33-video-package-script-video)
   - [3.4 Infographic & Data Visualizer](#34-infographic--data-visualizer-infographic)
   - [3.5 Presentation Slide Deck](#35-presentation-slide-deck-presentation)
   - [3.6 LinkedIn Thought Leadership Post](#36-linkedin-thought-leadership-post-linkedin_post)
   - [3.7 Twitter / X Micro-Thread](#37-twitter--x-micro-thread-twitter_thread)
4. [Platform Features & Core Capabilities](#4-platform-features--core-capabilities)
   - [4.1 Multi-Mode Input Ingestion](#41-multi-mode-input-ingestion)
   - [4.2 Universal In-line Editing](#42-universal-in-line-editing)
   - [4.3 Smart Format-Aware Clipboard Engine](#43-smart-format-aware-clipboard-engine)
   - [4.4 Content Verification & Upload Lifecycle](#44-content-verification--upload-lifecycle)
   - [4.5 Presentation PDF Download & Export to Canva](#45-presentation-pdf-download--export-to-canva)
   - [4.6 Contextual AI Prompt Dock & Live Refinements](#46-contextual-ai-prompt-dock--live-refinements)
   - [4.7 Dual Pipeline: Offline Demo Mode vs Live n8n Engine](#47-dual-pipeline-offline-demo-mode-vs-live-n8n-engine)
   - [4.8 Workspace Customization & Multi-Language Support](#48-workspace-customization--multi-language-support)
   - [4.9 Project & Session Management](#49-project--session-management)
5. [Component Map & Directory Reference](#5-component-map--directory-reference)

---

## 1. System Architecture & Design System

Saarvistar is built on **Next.js (App Router)**, **TypeScript**, **Tailwind CSS**, and **Zustand**. It adheres to high-density, enterprise-grade dark/light aesthetics inspired by Material Design 3 and modern developer intelligence dashboards.

### Typography
- **Primary Interface Font**: `Inter` (`--font-inter`) for crisp data hierarchy, counters, controls, and labels.
- **Editorial / Reading Font**: `Lora` (`--font-lora`) for narrative long-form bodies, advisories, and summaries.
- **Iconography**: `lucide-react` combined with Google `Material Symbols Outlined`.

### Color Tokens & Elevation
- **Surfaces**: `bg-surface-dim`, `bg-surface`, `bg-surface-container-low`, `bg-surface-container`, `bg-surface-container-high`, `bg-surface-container-highest`.
- **Primary Accents**: Vivid emerald/cyan/teal tokens (`text-primary`, `bg-primary`, `bg-secondary-container`) evoking trust and precision.
- **Severity Tokens**:
  - `CRITICAL`: Red (`bg-red-500/15`, `text-red-400`, `border-red-500/30`)
  - `HIGH`: Amber/Orange (`bg-orange-500/15`, `text-orange-400`, `border-orange-500/30`)
  - `MEDIUM`: Yellow (`bg-yellow-500/15`, `text-yellow-400`, `border-yellow-500/30`)
  - `LOW`: Emerald/Green (`bg-emerald-500/15`, `text-emerald-400`, `border-emerald-500/30`)

---

## 2. Global Application Layouts

```
┌────────────────────────────────────────────────────────────────────────┐
│ TopHeader.tsx (Fixed Header: Hamburger, Toast Capsule, New Chat)       │
├─────────────────┬──────────────────────────────────────────────────────┤
│                 │ Main Content Area                                    │
│ Sidebar.tsx     │                                                      │
│ (Collapsible)   │ [Home Composer View] (EmptyState.tsx)                │
│                 │   OR                                                 │
│ • Brand Logo    │ [Transformation Workspace View] (app/chat/[chatId])  │
│ • New Chat CTA  │   ├── Header Bar (Document tag, Copy/Download PDF)   │
│ • Chat History  │   ├── FormatTabs.tsx (Vertical Dock)                 │
│ • Projects Tree │   ├── OutputPanel.tsx / ContentCard.tsx              │
│ • User Profile  │   │     └── [Active Format Component]                │
│ • Preferences   │   └── PromptDock.tsx (Floating Contextual Refiner)   │
└─────────────────┴──────────────────────────────────────────────────────┘
```

### 2.1 Root Layout Shell
- **File**: [`app/layout.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/app/layout.tsx)
- **Role**: Wraps all application routes in a persistent layout shell.
- **Key Features**:
  - Injects Google Font classes (`Inter`, `Lora`) and Google Material Symbols stylesheet.
  - Mounts `<ThemeSync />` to dynamically synchronize `dark`, `light`, or `system` themes from local store.
  - Houses `<Sidebar />` and `<TopHeader />` with a responsive wrapper (`main-content-wrapper`) that smoothly animates margins when the sidebar collapses/expands (`md:pl-64` to `0`).

---

### 2.2 Collapsible Sidebar Navigation
- **File**: [`components/layout/Sidebar.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/layout/Sidebar.tsx)
- **Role**: Persistent navigation drawer managing past transformation sessions, project groupings, and user workspace preferences.
- **Layout Modules**:
  1. **Brand Identity Header**: Displays animated `SarvistarMark` emblem, product name, and toggle collapse button.
  2. **Quick Transformation CTA**: Prominent "+ New Transformation" button with `Ctrl/⌘ + K` shortcut indicator.
  3. **Pinned & Recent Sessions**:
     - Chronological session list displaying document names, active formats, and relative timestamps.
     - Active session highlight with glowing border and indicator pip.
     - Pinning mechanism (`Pin` icon) to keep priority sessions anchored to the top.
     - Kebab context menu (`...`) supporting **Inline Renaming** and **Delete** with confirmation.
  4. **Projects Tree**:
     - Collapsible folder tree for organizing sessions by organization, team, or incident.
     - Inline project creator with "+ New Project" input.
     - Kebab menu per project allowing project deletion and session regrouping.
  5. **User Profile & Footer**:
     - User avatar, name, and email pill (`user@enterprise.internal`).
     - **Language Selector Dropdown**: Instant toggle between **English**, **हिन्दी (Hindi)**, and **मराठी (Marathi)**.
     - **Preferences Trigger**: Opens the Workspace Preferences modal.
     - **Logout**: Session termination trigger.

---

### 2.3 Top Application Header
- **File**: [`components/layout/TopHeader.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/layout/TopHeader.tsx)
- **Role**: Lightweight, glassmorphic top navigation bar with contextual feedback.
- **Layout Elements**:
  - **Drawer Toggle**: Accessible hamburger menu icon shown when sidebar is collapsed or on mobile screens.
  - **Animated Toast Capsule**: Centered/floating pill displaying real-time feedback (e.g., `"Content verified ✓"`, `"Copied to clipboard"`, `"Changes saved"`).
  - **Quick "+ New Chat" CTA**: Fast-reset button returning to composer home.

---

### 2.4 Home Composer / Empty State Layout
- **File**: [`components/chat/EmptyState.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/EmptyState.tsx)
- **Role**: The landing composer where users supply incident reports, define instructions, and select target output channels.
- **Layout Sections**:
  1. **Hero Title & Badge**: "Transform Enterprise Intelligence into Synchronized Narrative Channels" with subtle glowing backdrop.
  2. **Multi-Mode Input Composer**:
     - Dual-input canvas: primary Source Text textarea + secondary human directive prompt textarea.
     - File drag-and-drop ingestion dropzone supporting PDF, DOCX, TXT, and images.
     - Attached file pills displaying file icons, name, file size, thumbnail preview, and removal button (`X`).
  3. **Target Format Selector Dropdown**:
     - Multi-select format picker with "Select All" / "Clear All" toggles.
     - Supported format pills (`Executive Summary`, `Public Advisory`, `LinkedIn Post`, `Twitter/X Thread`, `Video Package`, `Infographic`, `Presentation`).
  4. **Composer Footer Controls**:
     - Attachment triggers (Paperclip / Upload PDF).
     - Security indicator pill (`End-to-End Enterprise Encryption`).
     - Submit button with dynamic loading state (`Transforming...` with spinning spinner).
  5. **Quick-Start Preset Chips**:
     - Clickable sample templates:
       - *"Fictional M365 Credential Compromise"*
       - *"Executive Ransomware Post-Mortem"*
       - *"Q3 Zero-Day Vulnerability Disclosure"*

---

### 2.5 Transformation Workspace Layout
- **File**: [`app/chat/[chatId]/page.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/app/chat/%5BchatId%5D/page.tsx)
- **Role**: The core inspection and editing workbench once a document has been processed.
- **Layout Structure**:
  1. **Workspace Top Header**:
     - Transformation title and editable session moniker.
     - Document metadata badge showing source filename, file type icon, and timestamp.
     - Contextual Action Button: **Download PDF** for presentations; **Copy to Clipboard** for text formats.
  2. **Dual-Pane Work Area**:
     - **Left Pane (`FormatTabs.tsx`)**: Vertical icon dock displaying available transformed outputs with active glow indicator and tooltip titles.
     - **Right Pane (`OutputPanel.tsx` & `ContentCard.tsx`)**: The active format's rendering canvas, hosting specialized layouts, inline edit tools, verification pills, and export options.
  3. **Floating Contextual Prompt Dock (`PromptDock.tsx`)**:
     - Persistent bottom docking bar pinned above the footer.
     - Dynamic hint text that adapts automatically to the selected tab.
     - Text input and submission arrow for prompting iterative refinements.

---

## 3. Specialized Output Formats & Layout Blueprints

Saarvistar produces **7 production-grade output layouts** plus a fallback view:

| Format ID | Display Title | Target Audience | Primary Focus |
| :--- | :--- | :--- | :--- |
| `structured_advisory` | **Public Advisory** | CISO, SecOps, Public Stakeholders | Auditable technical incident breakdown with severity tags, recommendations, and action ownership table |
| `exec_summary` | **Executive Summary** | C-Suite, Board of Directors | High-level business impact, strategic takeaways, and organizational risk summary |
| `video` | **Video Package** | Internal Comms, Townhalls, Training | Scene-by-scene script with timing, voiceover narration, and visual cues |
| `infographic` | **Infographic** | Visual Dashboards, Social, Reports | Key metric counters, delta trends, milestone timeline, and core takeaways |
| `presentation` | **Presentation Deck** | Board Meetings, Stakeholders | Slide-by-slide deck with bullet hierarchy, layout indicators, and speaker notes |
| `linkedin_post` | **LinkedIn Post** | Industry Peers, Professional Network | Engaging professional thought-leadership post with hashtags and call to action |
| `twitter_thread` | **Twitter / X Thread** | Tech Community, Public Feed | Numbered micro-posts designed for virality, engagement, and crisp updates |

---

### 3.1 Structured Advisory Document (`structured_advisory`)
- **Component**: [`components/chat/formats/StructuredAdvisory.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/StructuredAdvisory.tsx)
- **Layout Breakdown**:
  - **Classification Banner**: Editable pill displaying classification level (e.g. `RESTRICTED / TLP:AMBER`, `CONFIDENTIAL`).
  - **Executive TL;DR**: Accentuated callout container with border accent detailing the incident genesis and blast radius.
  - **Key Findings**: Bulleted technical findings extracted directly from endpoint telemetry.
  - **Prioritized Narrative Sections**: Multi-paragraph sections with distinct severity pill badges (`CRITICAL`, `HIGH`, `MEDIUM`, `LOW`).
  - **Numbered Recommendations**: Ordered actionable protocols for prevention and remediation.
  - **Action Items Grid**: Responsive 3-column table detailing **Owner / Team**, **Action Description**, and **Deadline / Status**.

---

### 3.2 Executive Summary (`exec_summary`)
- **Component**: [`components/chat/formats/ExecutiveSummary.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/ExecutiveSummary.tsx)
- **Layout Breakdown**:
  - **Header & Tone Badge**: Title with tone indicator (e.g., `Authoritative`, `Objective`, `Urgent`).
  - **Executive Highlights**: Grid of key strategic takeaway cards with primary accent highlights.
  - **Deep-Dive Strategic Sections**: Heading-and-body sections focusing on business risk, financial impact, and governance posture.

---

### 3.3 Video Package Script (`video`)
- **Component**: [`components/chat/formats/VideoPackage.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/VideoPackage.tsx)
- **Layout Breakdown**:
  - **Production Metadata**: Target platform pill (e.g., `Executive All-Hands`, `LMS Awareness`) and estimated running time (`90s`).
  - **Opening Hook Card**: Highlighted quotation box with the dramatic script opening.
  - **Scene Breakdown Cards**:
    - Scene number header and scene title.
    - Estimated duration timestamp (e.g., `0:00 - 0:20`).
    - **Voiceover Narration**: Primary spoken text with inline textarea editing.
    - **Visual Direction Box**: Shaded cue card indicating camera angles, graphic animations, and on-screen text.
  - **Call To Action (CTA)**: Concluding instructions for the video audience.

---

### 3.4 Infographic & Data Visualizer (`infographic`)
- **Component**: [`components/chat/formats/Infographic.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/Infographic.tsx)
- **Layout Breakdown**:
  - **Header & Subtitle**: High-level visual document branding.
  - **Stat Counter Cards**: 3-to-4 column responsive grid of KPI cards with numerical values, units, labels, and trend arrows (📈 Up, 📉 Down, ➖ Neutral).
  - **Executive Takeaways**: Bulleted highlight box summarizing core data conclusions.
  - **Chronological Incident Timeline**: Vertical stepped timeline with timestamps, milestone badges, and incident event details.
  - **Visual Export & PDF Preview Modal**: In-app modal displaying PDF preview with direct PDF download button.
  - **Source Attribution Footer**: Verified source telemetry badge.

---

### 3.5 Presentation Slide Deck (`presentation`)
- **Component**: [`components/chat/formats/Presentation.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/Presentation.tsx)
- **Layout Breakdown**:
  - **Audience Banner**: Target executive audience specifier (e.g., `Executive Leadership & Technical Advisory Board`).
  - **Horizontal Slide Carousel / Thumbnail Navigator**:
    - Slide cards with slide numbering, titles, and layout type tags (`🏷 Title`, `📄 Content`, `⬛⬛ Two-Col`, `💬 Quote`, `📊 Data`).
    - Stepping controls (Previous / Next).
  - **Active Slide Canvas**:
    - 16:9 presentation preview container.
    - Slide headline with layout badge.
    - Bullet point hierarchy with inline editable bullet items.
  - **Collapsible Speaker Notes Drawer**:
    - Presenter's private speaking points and talking directives.
  - **Export Suite**:
    - Direct PDF download button (`Download PDF`).
    - Full-screen view trigger.
    - One-click **Export to Canva** action.

---

### 3.6 LinkedIn Thought Leadership Post (`linkedin_post`)
- **Component**: [`components/chat/formats/LinkedInPost.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/LinkedInPost.tsx)
- **Layout Breakdown**:
  - **Author Header**: Persona badge with avatar and designation.
  - **Narrative Body**: Multi-paragraph formatted post with paragraph-by-paragraph inline editing.
  - **Pull Quote Callout**: Highlighted quotation card emphasizing core insight.
  - **Takeaway Bullets**: Formatted bullet points for scannability.
  - **Engagement Question & Concluding Thought**: Conversation-starter prompt.
  - **Hashtags Bar**: Clickable `#SecurityAwareness`, `#CyberDefense` pills.
  - **Character Count Indicator**: Live counter ensuring compliance with LinkedIn character limits.

---

### 3.7 Twitter / X Micro-Thread (`twitter_thread`)
- **Component**: [`components/chat/formats/TwitterThread.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/TwitterThread.tsx)
- **Layout Breakdown**:
  - **Micro-Post Cards**: Stacked tweet cards with sequence indicators (`1/5`, `2/5`, etc.).
  - **Role Pills**: Contextual tag per tweet (`Hook`, `Details`, `Impact`, `Remediation`, `Takeaway`).
  - **Character Meter**: Visual 280-character limit counter per tweet.
  - **Copy Actions**: Individual tweet copy button alongside full-thread copy button.

---

## 4. Platform Features & Core Capabilities

### 4.1 Multi-Mode Input Ingestion
Saarvistar supports three distinct operational ingestion modes:
1. **Mode 1 (Text Only)**: Directly paste raw incident text, logs, or briefing memos into the composer.
2. **Mode 2 (File Upload Only)**: Upload PDF, DOCX, TXT, or images. The backend extracts telemetry and builds structured narrative channels.
3. **Mode 3 (Hybrid: Text + File)**: Combines an authoritative source document (e.g., vendor incident report PDF) with human-authored executive instructions (e.g., *"Focus on remediation timelines and omit IP addresses"*).

---

### 4.2 Universal In-line Editing
- Every output format incorporates an **In-line Edit Toggle** in the [`ContentCard.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/ContentCard.tsx) header.
- Switching to edit mode transforms static text, bullet points, table cells, voiceovers, and speaker notes into live input fields.
- Clicking **Done** automatically commits and persists changes to the global **Zustand store** (`useAppStore`), maintaining edits across tab switches and browser refreshes.

---

### 4.3 Smart Format-Aware Clipboard Engine
The **Copy** action formats text according to the specific conventions of the active output format:
- **Structured Advisory**: Outputs formatted Markdown with headings, bulleted findings, and an ASCII action items matrix.
- **Video Package**: Copies a production script transcript with timestamps, narration, and visual cues.
- **Infographic**: Copies a structured summary containing KPI values, trend directions, and chronological timeline events.
- **Presentation**: Generates a slide-by-slide markdown transcript complete with slide titles, bullets, and speaker notes.
- **Twitter Thread**: Formats clean tweet breaks (`1/N`) separated by dividers.

---

### 4.4 Content Verification & Upload Lifecycle
Located in the format toolbar, the **Verify** system provides a multi-stage approval workflow:
1. **Idle State (`Verify`)**: Content is unverified; displays shield icon.
2. **Verification In-Progress (`Verifying…`)**: Animated pulse showing cryptographic/audit verification.
3. **Verified State (`Verified ✓`)**: Confirmed indicator badge with checkmark.
4. **Publish / Upload State (`Upload`)**: Action button enabling dispatch of the approved intelligence artifact to enterprise downstream destinations.

---

### 4.5 Presentation PDF Download & Export to Canva
Designed for executive presentation readiness:
- **Download PDF**: Downloads a pre-rendered, professional slide deck PDF directly to the user's workstation.
- **Export to Canva**:
  1. Triggers the local PDF download.
  2. Displays an informative toast notification.
  3. Automatically opens Canva's upload import portal (`https://www.canva.com/upload`) in a new tab, allowing users to import and customize slides within their design system.

---

### 4.6 Contextual AI Prompt Dock & Live Refinements
The bottom [`PromptDock.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/PromptDock.tsx) dynamically alters its behavior and placeholder hints based on the active tab:
- *Structured Advisory*: `"Ask Saarvistar to adjust classification, modify action items, or reprioritize sections..."`
- *Video Package*: `"Ask Saarvistar to adjust narration timing, add visual cues, or sharpen opening hook..."`
- *Infographic*: `"Ask Saarvistar to emphasize specific metrics, update timeline milestones, or change focus..."`
- *Presentation*: `"Ask Saarvistar to adjust slide count, rewrite speaker notes, or change bullet structure..."`

Users can prompt targeted modifications (e.g., *"Make the tone more urgent"* or *"Shorten the video script to 60 seconds"*), and only the active format will regenerate while preserving the other channels.

---

### 4.7 Dual Pipeline: Offline Demo Mode vs Live n8n Engine
- **Deterministic Demo Mode (`DEMO_MODE = true`)**:
  - Ships with realistic cybersecurity incident simulation data ([`lib/demoData.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/lib/demoData.ts)).
  - Allows full offline evaluations, live demos, and UI testing with zero latency and no external credentials.
- **Live Enterprise n8n Pipeline (`DEMO_MODE = false`)**:
  - Handled by [`app/api/transform/route.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/app/api/transform/route.ts).
  - Routes multipart form data and JSON payloads to an n8n webhook endpoint with `120s` timeout handling and `X-Webhook-Secret` authentication.
  - Adaptive parser ([`lib/n8nParser.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/lib/n8nParser.ts)) normalizes format aliases and parses structured JSON or markdown outputs.

---

### 4.8 Workspace Customization & Multi-Language Support
Accessed via the Sidebar user menu:
- **Theme Modes**:
  - `Dark` (default high-contrast dark theme).
  - `Light` (clean enterprise light mode).
  - `System` (follows OS preference).
- **Interface Density**:
  - `Comfortable` (spacious padding for review).
  - `Compact` (high-density layout for technical incident analysts).
- **Localization**:
  - Supports English (`en`), Hindi (`hi`), and Marathi (`mr`) interface switching.

---

### 4.9 Project & Session Management
- **Persistence**: All transformation history, edits, and active formats are saved to local storage via Zustand (`saarvistar-sessions-v3`).
- **Pinning**: Keep critical post-mortems pinned to the top of the sidebar.
- **Renaming**: Click "Rename" in the session menu to customize session names.
- **Project Groups**: Group related incident sessions under folder containers (e.g. `Incident_Northstar_2026`).

---

## 5. Component Map & Directory Reference

```
Saarvistar/
├── app/
│   ├── api/
│   │   └── transform/
│   │       └── route.ts               # Webhook routing & transformation API
│   ├── chat/
│   │   └── [chatId]/
│   │       └── page.tsx               # Transformation workspace screen
│   ├── layout.tsx                     # Global HTML shell & font injector
│   └── page.tsx                       # Landing page mounting EmptyState
│
├── components/
│   ├── chat/
│   │   ├── formats/
│   │   │   ├── ExecutiveSummary.tsx   # Executive summary layout
│   │   │   ├── Infographic.tsx        # Infographic & KPI metrics layout
│   │   │   ├── LinkedInPost.tsx       # LinkedIn post layout
│   │   │   ├── Presentation.tsx       # Slide deck & carousel layout
│   │   │   ├── PublicAdvisory.tsx     # Classic advisory layout
│   │   │   ├── StructuredAdvisory.tsx # Priority advisory & action matrix
│   │   │   ├── TwitterThread.tsx      # Multi-post thread layout
│   │   │   └── VideoPackage.tsx       # Production video script layout
│   │   │
│   │   ├── ContentCard.tsx            # Card wrapper (Edit, Verify, Copy, Canva)
│   │   ├── EmptyState.tsx             # Home composer & multi-mode input
│   │   ├── FormatTabs.tsx             # Vertical format dock switcher
│   │   ├── OutputPanel.tsx            # Active format orchestrator
│   │   └── PromptDock.tsx             # Floating contextual prompt refiner
│   │
│   ├── common/
│   │   ├── CopyButton.tsx             # Clipboard copy button with toast
│   │   ├── SarvistarMark.tsx          # SVG brand logo mark
│   │   ├── ThemeSync.tsx              # Synchronizes DOM theme classes
│   │   └── WorkspacePreferencesModal.tsx # Theme, density, and language modal
│   │
│   └── layout/
│       ├── Sidebar.tsx                # Collapsible sidebar with projects & history
│       └── TopHeader.tsx              # Floating top bar with toast & new chat CTA
│
├── lib/
│   ├── demoData.ts                    # Deterministic offline test fixtures
│   ├── n8nParser.ts                   # Adaptive response parsing & alias resolution
│   ├── store.ts                       # Zustand global state management
│   └── types.ts                       # TypeScript interfaces for all formats
│
├── public/
│   ├── infographic.pdf                # Pre-rendered sample infographic PDF
│   ├── infographic-preview.png        # Infographic visual preview
│   └── presentation.pdf               # Pre-rendered executive presentation deck
│
├── styles/
│   └── globals.css                    # Tailwind tokens, fonts, & animations
│
├── features.md                        # High-level capabilities summary
└── LAYOUTS_AND_FEATURES.md            # Comprehensive layout & feature architecture specification
```
