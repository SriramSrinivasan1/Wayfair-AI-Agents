# 🛋️ Wayfair Rugs Market Intelligence — AI Agent System

![n8n](https://img.shields.io/badge/Workflow-n8n-EA4B71?style=flat-square)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-API-34A853?style=flat-square&logo=googlesheets&logoColor=white)
![AI Agents](https://img.shields.io/badge/AI%20Agents-5%20Connected-10B981?style=flat-square)
![Status](https://img.shields.io/badge/Status-Complete-22C55E?style=flat-square)

> **5 connected AI agents. Live competitor monitoring. Decision-ready insights — automatically.**

📽️ **[Watch the AI Agent Demo Presentation](https://drive.google.com/file/d/1pzRaQG9wtIkUkZP-wvOcaWC1L0_lt1Ip/view?usp=sharing)**

Built for Wayfair's Rugs category team to eliminate the manual effort behind trend discovery, competitor tracking, and content ideation. Instead of periodic ad hoc research, the system runs continuously and surfaces structured, actionable intelligence to a single live dashboard.

---

## 📋 Table of Contents

- [Business Problem](#-business-problem)
- [Solution Architecture](#-solution-architecture)
- [The 5 Agents](#-the-5-agents)
- [Key Findings](#-key-findings)
- [Repo Structure](#-repo-structure)
- [Setup & Usage](#-setup--usage)
- [Future Improvements](#-future-improvements)
- [Limitations & Disclaimers](#-limitations--disclaimers)

---

## 🎯 Business Problem

Wayfair's Rugs merchandising and marketing teams needed a scalable way to:

- **Track competitor pricing and assortment changes** across Amazon, Target, and other retailers — without spending hours on manual PDP reviews
- **Spot emerging design trends** before they peak, so assortment and content decisions stay ahead of the market
- **Convert market signals into ready-to-use content** (blog drafts, social captions, campaign hooks) without burdening the marketing team with raw data

The status quo relied on periodic manual research, creating gaps in visibility and delays in decision-making.

---

## 🧠 Solution Architecture

Built 5 connected AI agents that feed into a single Market Intelligence Dashboard. Each agent has a defined role, structured output format, and hands off to the next in the pipeline.

```
┌─────────────────────────────────────────────────────────────────┐
│                  WAYFAIR RUGS INTELLIGENCE SYSTEM               │
│                                                                 │
│  [Agent 1]          [Agent 2]          [Agent 3]                │
│  Moodboard    ──►   Trend          ──► Competitor               │
│  Generator          Discovery           Monitoring              │
│  (Gemini API)       (Amazon/Google      (PDP Scraping +         │
│                     /RSS Feeds)         JSON Extraction)        │
│                                              │                  │
│                                              ▼                  │
│                                       [Agent 4]                 │
│                                       AI Insights &             │
│                                       Content Generation        │
│                                       (Blog / Social /          │
│                                        Campaign Ideas)          │
│                                              │                  │
│                                              ▼                  │
│                              ┌───────────────────────────┐      │
│                              │  [Agent 5]                │      │
│                              │  Market Intelligence      │      │
│                              │  Dashboard (Google Sheets)│      │
│                              │  Auto-refreshing · Live   │      │
│                              └───────────────────────────┘      │
└─────────────────────────────────────────────────────────────────┘
```

All agents write structured JSON outputs to a centralized data store. The dashboard auto-updates via connected n8n workflows, with conditional formatting highlighting priority insights.

---

## 🤖 The 5 Agents

### Agent 1 — Moodboard Generator
**Design Prompt → AI-Curated Visual Mood**
 
Transforms short text prompts into AI-generated moodboard briefs using Google Gemini, structured for immediate use by the design team. Each brief includes key rug designs, material close-ups, styled interior spaces, and complementary decor elements.
 
| Parameter | Detail |
|-----------|--------|
| Input | Short style + material prompt (e.g. "Modern colored rug.") |
| Output | Structured moodboard brief with 4 design sections |
| Model | Google Gemini Chat Model via n8n |
| Workflow | n8n — chat trigger → AI Agent → Gemini |
 
**Prompt Tips:** Keep prompts short and specific — `"eco-friendly jute rugs"`, `"modern geometric rug"`. Avoid subjective adjectives like "beautiful" or "nice."
 
**Sample Moodboard Output** *(prompt: "Bohemian rugs, neutral tones")*
 
[Moodboard Sample](https://drive.google.com/file/d/11x_ItM9PIpcpFgQ-5uknYipmGPZbqTXk/view?usp=sharing)
 
**n8n Workflow:**
 
[Agent 1 Workflow](https://drive.google.com/file/d/1XyCB-lMYrqDrP-N_B2SgaYMFkkt9TmVA/view?usp=sharing)

---

### Agent 2 — Rugs Trend Discovery
**Multi-Source Signals → Emerging Trend Reports**

Identifies emerging vs. sustained rug trends by ingesting live data from Amazon, Google Search, and RSS industry feeds — then using Gemini to generate a structured HTML trend report, updated weekly.

| Parameter | Detail |
|-----------|--------|
| Input | Chat message (keyword, topic, or Amazon URL) |
| Sources | Amazon PDPs & collections · Google Custom Search API · 4 RSS feeds |
| RSS Feeds | Cover Magazine · Floor Trends Magazine · Google Rug Trends · Nazmiyal Antique Rugs |
| Output | Structured HTML trend report (trend name, material, pattern, style, price bucket, source citations) |
| Frequency | Last 7 days of signals only (auto-filtered) |
| Workflow | n8n — chat trigger → intent classification → parallel data collection → merge → Gemini analysis → HTML output |

**How it works:** The agent first classifies user intent via Gemini — if trend-related, it fans out in parallel to scrape Amazon product pages, run a Google Search, and pull from 4 RSS feeds simultaneously. All results are merged, normalized, and passed to Gemini, which produces a formatted HTML trend report with source citations. The report is also pushed to the Agent 5 dashboard.

**Workflow Overview:**

[Agent 2 Workflow](https://drive.google.com/file/d/14ELgjE-FWKzJcKOJNrdVzIoCbQDL_C1h/view?usp=sharing)
[Market Trend Report](https://drive.google.com/file/d/12y086saZz0xfRjsuba22VMPZIhOr4AsZ/view?usp=sharing)

---

### Agent 3 — Competitor Monitoring
**Competitor Pages → Actionable Marketing Intelligence**
 
Scrapes Wayfair's own rug assortment as a baseline, then pulls competitor data from Amazon and Ruggable in parallel. A swappable Gemini system message reinterprets the same dataset through different business lenses — pricing, messaging, features, or whitespace — producing a structured HTML competitive intelligence report.
 
| Parameter | Detail |
|-----------|--------|
| Input | Chat message (keyword, topic, or Amazon URL) |
| Competitors Monitored | Amazon (PDPs + bestsellers fallback) · Ruggable (category → product pages) |
| Baseline | Wayfair rug category page (title · SKU · price · rating · reviews) |
| Output | HTML report (competitor · price · rating · PDP highlights · Wayfair gap · suggested action) |
| Scraping Tool | Firecrawl API (rate-safe, browser-rendered HTML) |
| Workflow | n8n — chat trigger → parallel scraping → merge + deduplicate → Gemini analysis → HTML output |
 
**How it works:** Wayfair's own category page is scraped first to establish an internal baseline. Amazon and Ruggable are scraped in parallel — Amazon supports both direct product URLs and a bestsellers fallback when no links are provided. All data is merged, deduplicated by ASIN, and passed to Gemini. The active system message controls what the agent focuses on — swapping it simulates how different Wayfair teams (merchandising, marketing, or leadership) would analyze the same dataset.
 
**Swappable analysis lenses:**
- **Pricing** — where does Wayfair overlap or get undercut?
- **Messaging** — tone of voice, emotional framing, keyword gaps
- **Features** — washability, materials, certifications competitors highlight
- **Whitespace** — assortment gaps Wayfair could fill
**Workflow Overview:**
 
[Agent 3 Workflow](https://drive.google.com/file/d/12WlHUz7AN04w79WUnjTzHSREnr3eNtzY/view?usp=sharing)
[Competitive Analysis Report](https://drive.google.com/file/d/10Ex2Sgm64NE5RZNXlE1R3udArCpB7vZ3/view?usp=sharing)


---

### Agent 4 — AI Insights & Content Generation
**Trend + Competitor Signals → Decision-Ready Content**

Scrapes Amazon and Walmart product data in parallel, passes it through two chained Gemini agents — the first generates structured market signals, the second translates those signals into Wayfair-ready content. Outputs are saved to Supabase for persistence and pushed to the Agent 5 dashboard.

| Parameter | Detail |
|-----------|--------|
| Input | Chat message with Amazon and/or Walmart product or collection URLs |
| Sources | Amazon (PDPs · collections · bestsellers) · Walmart (best-selling rugs category) |
| Agent 1 Output | Structured JSON signals — trend signal · competitor signal · style signal |
| Agent 2 Output | Blog strategy · key insights · Instagram caption ideas |
| Storage | Supabase (create or update row — persists insights across runs) |
| Workflow | n8n — chat trigger → parallel scraping → merge → signal agent → content agent → HTML + Supabase |

**How it works:** URLs are extracted and classified from the chat input. Amazon and Walmart are scraped in parallel — Amazon supports both direct product pages and collection/bestseller pages, Walmart targets best-selling rug listings. All data is cleaned, deduplicated, and merged into a single dataset. The first Gemini agent analyzes the combined data and produces three structured JSON signals (trend, competitor, style). The second Gemini agent — acting as a Wayfair marketing strategist — translates those signals into brand-aligned content ideas. The final HTML report is downloadable and the insights are written to Supabase for the Agent 5 dashboard.

**Two-agent chain:**
- **Signal agent** — analyzes raw product data, enforces consistent JSON output structure
- **Content agent** — thinks like a Wayfair marketing strategist, produces blog strategy, key insights, and social captions

**Workflow Overview:**

[Agent 4 Workflow](https://drive.google.com/file/d/1LdfgTdVpZdlTZ3iNp58Qscs-QErSgqck/view?usp=sharing)
[Marketing Content Ideas](https://drive.google.com/file/d/1w0fhf533UbU501-SrlqfAoCeA2B3rHOf/view?usp=sharing)

---

### Agent 5 — Market Intelligence Dashboard
**All Agents → Single Live View for Wayfair's Rugs Team**

The integration layer that ties the entire system together. On a single trigger, it fetches the stored HTML reports from Agents 2, 3, and 4 out of Supabase, passes each through a dedicated Gemini model to extract structured insights, and appends the cleaned data into three tabs of a live Google Sheets dashboard.

| Parameter | Detail |
|-----------|--------|
| Trigger | Manual ("Execute workflow") or scheduled |
| Data Sources | Supabase — stored outputs from Agents 2, 3, and 4 |
| Processing | 3 parallel Gemini models — one per report type |
| Output | Google Sheets dashboard — 3 tabs, continuously appended |
| Dashboard Tabs | Trend Signals · Competitor Moves · All Insights |
| Workflow | n8n — manual trigger → Supabase fetch → Gemini read → structure JSON → write to Sheets |

**How it works:** On trigger, three Supabase nodes fetch the latest HTML reports generated by Agents 2, 3, and 4 in parallel. Each report is passed to a dedicated Gemini model that reads and interprets the HTML, identifying key patterns and signals relevant to Wayfair's strategy. A code node then cleans and flattens the output into a consistent JSON structure compatible with Google Sheets. Finally, three update nodes append the data into their respective dashboard tabs — Trend Signals, Competitor Moves, and All Insights — building a continuously growing record over time.

**Dashboard design principle:** Built for quick scanning, not deep analysis. Conditional formatting highlights priority insights so the Rugs team can act without reading every row.

**Workflow Overview:**

[Agent 5 Workflow](https://drive.google.com/file/d/155ymURibUfSXd-Sq9mGRLaIdGPnHJcQU/view?usp=sharing)

---

## 🔍 Key Findings

| Trend | Signal Strength | Price Range | Opportunity |
|-------|----------------|-------------|-------------|
| Washable indoor/outdoor rugs | 🔴 High | Sub-$200 | Wayfair underinvesting in washability content |
| Eco-friendly materials (jute, recycled fibers) | 🟡 Medium | $80–$300 | Competitors highlighting in PDPs; Wayfair silent |
| Geometric & neutral-toned designs | 🟡 Medium | Mid-price | Gaining traction across competitor mid-tier assortments |
| Vintage/antique authenticity | 🟢 Sustained | $300+ | High-end collector segment still underserved |

**Headline Result:** Reduced competitive pricing review time by **80%** — from multi-hour manual research to automated daily reports surfaced in the dashboard.

---

## 📁 Repo Structure

```
wayfair-rugs-market-intelligence/
│
├── agents/
│   ├── agent1_moodboard/
│   │   ├── workflow.json           # n8n workflow definition
│   │   └── sample_output/         # Example moodboard images
│   ├── agent2_trend_discovery/
│   │   ├── workflow.json
│   │   └── sample_output.json     # Example trend report
│   ├── agent3_competitor_monitoring/
│   │   ├── workflow.json
│   │   └── sample_output.json     # Example competitor analysis
│   ├── agent4_content_generation/
│   │   ├── workflow.json
│   │   └── sample_output.json     # Example blog/social content
│   └── agent5_dashboard/
│       ├── workflow.json
│       └── dashboard_screenshot.png
│
├── presentation/
│   └── Sriram_Wayfair_AI_Agent_Final_Presentation.pdf
│
├── requirements.txt
└── README.md
```

---

## ⚙️ Setup & Usage

### Prerequisites

```bash
python >= 3.10
n8n (self-hosted or cloud)
Google Sheets API credentials
Google Gemini API key
```

### Installation

```bash
git clone https://github.com/sriramprog/wayfair-rugs-market-intelligence.git
cd wayfair-rugs-market-intelligence
pip install -r requirements.txt
```

### Configuration

1. Add your API keys to a `.env` file (never commit this):
```
GEMINI_API_KEY=your_key_here
GOOGLE_SHEETS_CREDENTIALS=path/to/credentials.json
```

2. Import the `agents/` workflow JSON files into your n8n instance.

3. Connect the Google Sheets dashboard using the credentials above.

4. Trigger any agent manually or set up scheduled runs in n8n.

---

## 🚀 Future Improvements

- **SKU-level performance metrics** — link trends to actual sales and inventory data to quantify revenue impact
- **Social & influencer signals** — extend data sources beyond marketplaces to Instagram, TikTok, and Pinterest for earlier trend detection
- **Time-series trend velocity** — distinguish emerging vs. accelerating vs. plateauing trends with historical comparisons
- **Confidence scoring** — add evidence indicators to AI-generated insights for prioritization
- **Alert-based monitoring** — trigger notifications for competitor price changes, promotions, or new launches
- **Category expansion** — extend beyond rugs to other Wayfair home categories (lighting, furniture, bedding)
- **Role-specific recommendations** — generate separate insight views for merchandising, marketing, and leadership teams

---

## ⚠️ Limitations & Disclaimers

- **Data freshness:** Trend signals are based on publicly available marketplace data from Amazon, Google Search, and RSS feeds as of January 2026. Results may not reflect real-time market conditions.
- **Competitor data:** PDP scraping reflects point-in-time snapshots. Competitor pricing and assortments change frequently.
- **API limits:** The Google Gemini free tier caps image generation at 20 requests/day. Heavy usage requires a paid plan.
- **No internal Wayfair data:** This project uses only publicly available data. No proprietary Wayfair inventory, sales, or internal systems were accessed.
- **AI-generated content:** Blog drafts and social captions produced by Agent 4 are starting points and require human review before publication.

---
