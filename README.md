# 🛋️ Wayfair Rugs Market Intelligence — AI Agent System

![n8n](https://img.shields.io/badge/Workflow-n8n-EA4B71?style=flat-square)
![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-API-34A853?style=flat-square&logo=googlesheets&logoColor=white)
![AI Agents](https://img.shields.io/badge/AI%20Agents-5%20Connected-10B981?style=flat-square)
![Status](https://img.shields.io/badge/Status-Complete-22C55E?style=flat-square)

> **5 connected AI agents. Live competitor monitoring. Decision-ready insights — automatically.**

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

Transforms short text prompts (e.g., `"bohemian rugs, neutral tones"`) into AI-generated moodboards using the Google Gemini image generation API. Enables rapid visual trend exploration without manual curation.

| Parameter | Detail |
|-----------|--------|
| Input | Text prompt (style + material keywords, <10 words) |
| Output | AI-generated moodboard image grid |
| Model | Google Gemini Image Generation API |
| Limit | 20 generations/day (free tier) |

**Prompt Tips:** Keep prompts short and specific — `"eco-friendly jute rugs"`, `"modern geometric rug"`. Avoid subjective adjectives like "beautiful" or "nice."

---

### Agent 2 — Rugs Trend Discovery
**Multi-Source Signals → Emerging Trend Reports**

Monitors Amazon marketplace listings, Google Search results, and RSS feeds to identify and categorize rug design trends. Distinguishes emerging trends from sustained ones.

| Parameter | Detail |
|-----------|--------|
| Input | Amazon/marketplace pages · Google Search · RSS Feeds |
| Output | Trend name, category, material, pattern, style, feature, price bucket, supporting examples |
| Update Frequency | Weekly |

**Sample Trends Detected (Jan 2026):**
- Strong demand for **washable & durable indoor/outdoor rugs**
- Rising preference for **easy-care vintage-style rugs**
- High-end interest in **authentic antique and vintage pieces**

---

### Agent 3 — Competitor Monitoring
**Competitor PDPs → Actionable Gap Analysis**

Scrapes competitor product detail pages and bestseller listings to extract structured pricing, rating, feature, and launch signal data. Benchmarks against Wayfair's assortment to surface gaps and whitespace.

| Parameter | Detail |
|-----------|--------|
| Input | Competitor PDPs and bestseller pages |
| Output | Competitor name · Product/collection · Price & rating · PDP highlights · Launch signals · Wayfair gap + suggested action |
| Competitors Monitored | Amazon, Target, and other major home goods retailers |

**Sample Output Fields:**
```json
{
  "competitor": "Amazon",
  "product": "Modern Indoor-Outdoor Rug Collection",
  "price_range": "$45–$189",
  "rating": 4.4,
  "pdp_highlight": "Washable, pet-friendly, UV-resistant",
  "launch_signal": "New colorways added Q4 2025",
  "wayfair_gap": "Limited brand narrative on washability benefits",
  "suggested_action": "Create content series: washable rug lifestyle benefits"
}
```

---

### Agent 4 — AI Insights & Content Generation
**Trend + Competitor Signals → Decision-Ready Content**

Takes the structured outputs from Agents 2 and 3 and generates insight summaries, recommended actions, and ready-to-use content ideas (blog outlines, social captions, campaign concepts) — ranked by impact level.

| Parameter | Detail |
|-----------|--------|
| Input | Trend discovery output + competitor monitoring output |
| Output | Summary insight · Recommended action · Content idea (blog/social/campaign) · Impact level |

**Sample Content Output — Blog Strategy:**
> *"Spill-Proof Style: Why Wayfair's Washable Rugs Are Your New Best Friend"*  
> Target: Home decor enthusiasts, budget-conscious millennials  
> SEO Focus: washable rugs, easy clean rugs, pet-friendly rugs, neutral rugs

---

### Agent 5 — Market Intelligence Dashboard
**All Agents → Single Live View for the Rugs Team**

Consolidates all agent outputs into a single auto-refreshing Google Sheets dashboard with an Executive Summary tab and conditional formatting highlighting priority insights.

| Parameter | Detail |
|-----------|--------|
| Data Source | Structured JSON outputs from Agents 1–4 |
| Update Mechanism | n8n workflow automation |
| Design Principle | Quick scanning, not deep analysis |

**Dashboard Structure:**
- **Executive Summary tab** — top emerging trend, Wayfair gap, suggested action
- **Market Signals layer** — raw data with source URLs
- **Content Ideas tab** — blog/social/campaign drafts ready for review
- **Competitor Tracker tab** — pricing and PDP comparison table

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
├── notebooks/
│   └── market_intelligence_analysis.ipynb
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
