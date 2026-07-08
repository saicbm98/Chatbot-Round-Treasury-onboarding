# AI Automation & Job-Search Tooling

A portfolio of small AI-automation and job-search tools built with Claude Code, Perplexity, Bright Data, Apify, and Streamlit. Each project below lives in this repo and is described from its actual code, not from a spec sheet.

---

## NZ Outreach Researcher

*A Streamlit tool that turns "who works at this company" into a shortlisted, researched, drafted outreach email.*

`linkedin_research/outreach_researcher.py` runs a six-stage pipeline on one page: Perplexity's Agent API (`people_search` + `web_search`) discovers named people at a target company matching chosen personas, the user ticks a shortlist in an editable table, the same Apify `harvestapi` pipeline used by the sibling Activity Researcher (`chat_researcher.py`) deep-scrapes only the selected profiles, results export to CSV, and an embedded Claude chat drafts outreach emails using the scraped data as context. Career-history enrichment uses a three-tier fallback: Bright Data's async Web Scraper API is the primary source for experience/education/about, Apify's profile scraper fills in when Bright Data returns null career fields (a known behaviour of its dataset), and a Perplexity narrative lookup is the last resort when neither source has structured experience data. Posts and reposts always come from Apify regardless of which source served the profile. Research history optionally syncs to Supabase Storage so it survives Streamlit Community Cloud's container resets, falling back to a local JSON file otherwise.

## GTM Architecture

*A static, interactive HTML diagram of a proposed AI-driven go-to-market operations system for Round Treasury.*

`gtm-architecture.html` is a self-contained page (no build step, vanilla CSS/JS) showing a three-tier agent architecture: a "Master Rev Ops Analysis Agent" orchestrating Marketing, Sales, and Onboarding execution agents, with a Reporting Agent as a downstream sidecar consuming event streams and insights. Each agent card is clickable/hoverable to reveal what it does and its KPI trackers, and cards are explicitly tagged "Built ✓", "GAP" (not yet built or dependent on understanding an existing process), or "Human" (a manual handoff step, e.g. AE handoff, activation call). It documents a design rather than a running system — the only piece marked as actually built is the KYC sub-agent.

## Tower Insurance Operations Page

*A single-page, first-person write-up on process excellence and AI at Tower (an insurer), not an operational dashboard.*

`tower-operations.html` is styled as a set of expandable card bands: what's already built and working (an AI contact centre, digital-first underwriting, stable core pricing with newer climate-pricing innovation), where AI is applied today (catastrophe claims and the NHC interface, underwriting/claims triage), the harder problem of capturing frontline judgement into SOPs so AI can be layered on correctly, and the people side of resourcing that work. The subtitle states plainly that this is "my thinking" — it reads as a personal analysis/pitch document rather than a description of a system the author built.

## Kukke Slot Checker

*A polling bot that watches a government temple-booking portal for a specific cancellation slot and emails when one opens.*

`kukke_slot_checker.py` scrapes the ITMS Karnataka portal for Kukke Subrahmanya Temple, looking for Sarpa Samskara seva slots in May/June 2026. It uses Playwright to load the page and pull the `disableDates`/`booked_date_array` JS globals out of the rendered DOM (falling back to a raw `requests` + regex extraction if Playwright is unavailable), diffs the two lists to find genuinely open dates, and sends an HTML email via Gmail SMTP when one appears. It runs as a validate-once-then-optionally-loop CLI, polling every 3 minutes with a 30-minute cooldown per distinct set of open dates so it doesn't spam repeat alerts for the same slot.

## Rondo Chatbot

*The onboarding/support chatbot for Round Treasury UK — planned in `CHATBOT_PLAN.md`/`FLOWCHARTS.md`/`rondo-chatbot.drawio`, and actually implemented in `app.py` and `src/`.*

The planning docs lay out a three-segment design (prospects, active onboarding clients, established clients), a compliance-rules layer (capital-at-risk disclaimers, FCA/regulatory disclosures, data-handling statements), and an escalation engine that hands unresolved or sensitive conversations to a human rep via Slack/email. The implementation follows that plan closely: `src/system_prompt.py` builds a segment-aware system prompt from a static knowledge base, `src/chatbot.py` streams responses from Claude (`claude-opus-4-7`) via the Anthropic SDK and detects an `[ESCALATE|...]` tag the model is instructed to emit, and `src/escalation.py` parses that tag and posts a formatted Slack message with the last three conversation turns. `src/session.py` is an in-memory session store (explicitly noted as swappable for Redis/a database later), and `app.py` exposes it all as a FastAPI service with streaming (SSE) and non-streaming chat endpoints plus a demo widget at `/`.

## Portfolio Website

`index.html` is not a standalone portfolio page — it is a two-line HTML file that immediately meta-refreshes to `gtm-architecture.html`. There is no other portfolio/landing content in this repo.

---

## Tech stack

Python (FastAPI, Streamlit, argparse CLI), Anthropic Claude API (`claude-opus-4-7`, `claude-sonnet-4-6`), Perplexity Agent API (people_search + web_search), Apify (harvestapi LinkedIn actors, Google Search Scraper), Bright Data Web Scraper API, Playwright, `requests`/`httpx`, Supabase Storage (REST API), pandas, reportlab + Pillow (PDF report rendering), Slack Incoming Webhooks, Gmail SMTP, vanilla HTML/CSS/JS (for the two static diagram pages), and `python-dotenv` for local config.
