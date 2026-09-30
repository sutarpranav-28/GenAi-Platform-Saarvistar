# Saarvistar: Enterprise Narrative Transformation Engine — Features & Capabilities

Saarvistar transforms complex technical documents, security incident reports, and raw operational logs into targeted, audience-specific intelligence outputs.

---

## 🚀 Overview of Supported Output Types

Saarvistar supports **8 distinct output formats** catering to executive leadership, technical response teams, external stakeholders, media, and public audiences:

| Format ID | Display Name | Target Audience | Primary Focus |
| :--- | :--- | :--- | :--- |
| `structured_advisory` | **Public Advisory** | CISO, SecOps, Public Stakeholders | Comprehensive technical incident breakdown with severity tags, recommendations, and action item ownership |
| `exec_summary` | **Executive Summary** | C-Suite, Board of Directors | High-level business impact, strategic takeaways, and organizational risk summary |
| `video` | **Video Package** | Internal Comms, Townhalls, Training | Scene-by-scene script with timing, voiceover narration, and visual cues |
| `infographic` | **Infographic** | Visual Dashboards, Social, Reports | Key metric counters, delta trends, milestone timeline, and core takeaways |
| `presentation` | **Presentation Deck** | Board Meetings, Stakeholders | Slide-by-slide deck with bullet hierarchy, layout indicators, and speaker notes |
| `linkedin_post` | **LinkedIn Post** | Industry Peers, Professional Network | Engaging professional thought-leadership post with hashtags and call to action |
| `twitter_thread` | **Twitter / X Thread** | Tech Community, Public Feed | Numbered micro-posts designed for virality, engagement, and crisp updates |

---

## 🛠 Deep Dive: The Four New Output Types

### 1. Structured Advisory Document (`structured_advisory`)
A formal, auditable security advisory designed for technical and incident response teams.
- **Classification Banner**: Prominent classification badge (`RESTRICTED / TLP:AMBER`, `CONFIDENTIAL`, etc.) with inline editing.
- **Executive TL;DR**: Dedicated callout box summarizing incident genesis and blast radius.
- **Key Findings**: Bulleted technical findings extracted directly from endpoint telemetry.
- **Prioritized Sections**: Multi-paragraph narrative with priority pill badges:
  - 🔴 `CRITICAL`
  - 🟠 `HIGH`
  - 🟡 `MEDIUM`
  - 🟢 `LOW`
- **Numbered Recommendations**: Actionable preventive and remedial recommendations.
- **Action Items Grid**: Structured responsive table capturing **Owner**, **Action Description**, and **Deadline / Status**.
- **Component**: [`StructuredAdvisory.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/StructuredAdvisory.tsx)

### 2. Video Package (`video`)
A complete multi-scene production script for internal briefings, security awareness, or video communications.
- **Metadata Badges**: Target platform (e.g., Executive All-Hands, LMS) and target duration (e.g., 90s).
- **Opening Hook**: High-impact opening hook designed to capture viewer attention immediately.
- **Scene Breakdown Cards**:
  - Scene numbering and scene titles.
  - Timestamp duration range (e.g. `0:00 - 0:20`).
  - **Narration**: Voiceover transcript with inline textarea editing.
  - **Visual Direction**: Camera directions, graphic animations, and on-screen cues.
- **Call To Action (CTA)**: Clear concluding instructions for viewers.
- **Component**: [`VideoPackage.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/VideoPackage.tsx)

### 3. Infographic (`infographic`)
A data-dense visual representation of metrics, milestones, and impacts.
- **Stat Cards**: Grid of key statistical counters (e.g., `37 Users`, `6 Minutes`, `100% MFA`) with customizable units and trend indicators (📈 Up, 📉 Down, ➖ Neutral).
- **Executive Takeaways**: Highlighted key insights.
- **Incident Timeline**: Chronological vertical timeline with timestamps and milestone milestones.
- **Source Attribution / Footer**: Formal attribution badge.
- **Component**: [`Infographic.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/Infographic.tsx)

### 4. Presentation Deck (`presentation`)
An executive slide deck designed for leadership briefings and post-mortem reviews.
- **Audience Targeting**: Explicit audience specification (e.g., *Executive Leadership & Technical Advisory Board*).
- **Slide Carousel & Thumbnail Navigator**:
  - Horizontal slide picker with slide numbers, titles, and layout tags (`🏷 Title`, `📄 Content`, `⬛⬛ Two-Col`, `💬 Quote`, `📊 Data`).
  - Previous / Next slide stepping controls.
- **Active Slide Preview**:
  - Distinct slide container with slide title and layout badges.
  - Bullet point hierarchy with inline bullet editing.
- **Speaker Notes**: Dedicated collapsible speaker note section giving presenters exact talking points.
- **Component**: [`Presentation.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/Presentation.tsx)

---

## ⚡ Core Platform Capabilities

### 1. Unified Format Tabs & Navigation
- **Format Tabs Header** ([`FormatTabs.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/FormatTabs.tsx)): Displays active output formats with dedicated Lucide icons (`ShieldAlert`, `Video`, `BarChart2`, `Presentation`, `BookOpen`, `Megaphone`, etc.).
- **Live Counter**: Dynamically tracks `Format X of Y` active outputs.

### 2. Universal Inline Editing
- All output formats feature an **Inline Edit Toggle** in the [`ContentCard.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/ContentCard.tsx) header.
- Allows live changes to headlines, bodies, bullet points, scenes, and action items.
- Edits persist directly to Zustand session storage (`useAppStore`).

### 3. Smart Clipboard Copy
- Clicking **Copy** on any format produces cleanly structured plain text:
  - Markdown-formatted sections for **Structured Advisory**.
  - Scene-by-scene script transcript for **Video Package**.
  - Formatted stats and timeline for **Infographic**.
  - Slide titles, bullets, and speaker notes for **Presentation**.

### 4. Contextual Prompt Dock & Dynamic Refinements
- The bottom prompt dock ([`PromptDock.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/PromptDock.tsx)) detects the currently active tab and displays tailored refinement hints:
  - *Structured Advisory*: "Ask Saarvistar to adjust classification, modify action items, or reprioritize sections..."
  - *Video Package*: "Ask Saarvistar to adjust narration timing, add visual cues, or sharpen opening hook..."
  - *Infographic*: "Ask Saarvistar to emphasize specific metrics, update timeline milestones, or change focus..."
  - *Presentation*: "Ask Saarvistar to adjust slide count, rewrite speaker notes, or change bullet structure..."

### 5. Multi-Mode Input Composer
- **Mode 1 (Text Only)**: Paste or type incident report text.
- **Mode 2 (PDF Only)**: Upload PDF/DOCX/TXT; backend processes document directly.
- **Mode 3 (Text + PDF)**: Combines structured PDF report with supplemental human directives.

### 6. Robust Backend & n8n Pipeline
- **Adaptive Parser** ([`n8nParser.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/lib/n8nParser.ts)):
  - Normalizes aliases (`advisory_document` → `structured_advisory`, `slide_deck` → `presentation`, `video_script` → `video`, etc.).
  - Handles structured JSON payloads as well as markdown/unstructured string responses from LLMs.
- **Offline / Deterministic Demo Mode** ([`demoData.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/lib/demoData.ts)):
  - Ships with realistic cybersecurity incident simulation data for all 8 output formats.
  - Zero-latency preview for testing and demonstration without needing live webhook credentials.
- **Enterprise Webhook Router** ([`app/api/transform/route.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/app/api/transform/route.ts)):
  - 120-second connection timeout handling.
  - Support for `multipart/form-data` and JSON payloads.
  - `X-Webhook-Secret` token header support.

---

## 📁 Key File References

- **Type Definitions**: [`lib/types.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/lib/types.ts)
- **Global State Store**: [`lib/store.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/lib/store.ts)
- **Response Parser**: [`lib/n8nParser.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/lib/n8nParser.ts)
- **Demo Fixtures**: [`lib/demoData.ts`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/lib/demoData.ts)
- **Output Panel Orchestrator**: [`components/chat/OutputPanel.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/OutputPanel.tsx)
- **Format Components**:
  - [`StructuredAdvisory.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/StructuredAdvisory.tsx)
  - [`VideoPackage.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/VideoPackage.tsx)
  - [`Infographic.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/Infographic.tsx)
  - [`Presentation.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/Presentation.tsx)
  - [`ExecutiveSummary.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/ExecutiveSummary.tsx)
  - [`PublicAdvisory.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/PublicAdvisory.tsx)
  - [`LinkedInPost.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/LinkedInPost.tsx)
  - [`TwitterThread.tsx`](file:///c:/Users/Aarya%20Raut/OneDrive/Desktop/TECHTITANSXX/Saarvistar/components/chat/formats/TwitterThread.tsx)
