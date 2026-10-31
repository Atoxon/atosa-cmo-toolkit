# atosa-cmo-toolkit

Internal, dependency-light marketing-audit toolkit for ATOSA. Built by selectively
extracting **tested, working** components from external open-source "AI marketing
skills" repos — not by installing them wholesale. See `provenance/SOURCES.md` for
exactly what was taken from where, under what license, and why the rest was rejected.

## Principle: Idea → Test → Data → Insight

Every tool here was run against a **real ATOSA URL** before being accepted into this
repo. A tool that only "looks correct" in someone else's README is not evidence —
it is a hypothesis. See `audit-tools/cro-audit/TEST-LOG.md` for the actual run log,
bugs found, and real output.

## Structure

```
atosa-cmo-toolkit/
├── audit-tools/
│   ├── cro-audit/          # Landing-page CRO scorer (8 dimensions, 0 API keys)
│   └── offer-builder/      # (planned) offer-scoring checklist, Hormozi value equation
├── growth-frameworks/
│   └── marketing-audit-rubric.md   # 6-dimension weighted audit checklist (manual use)
└── provenance/
    └── SOURCES.md          # What was taken from which repo, license, commit hash
```

## What this is NOT

- Not a copy of any single upstream repo — each upstream repo was audited for license,
  dependencies, telemetry, and API-key requirements before anything was extracted.
- Not a place for unverified "AI marketing" claims — any score/output here is a
  **heuristic signal**, not a substitute for real conversion/revenue data from
  ATOSA's own CRM/analytics.
- Not wired to any third-party SaaS (HubSpot, Instantly, RB2B, Gong, Ahrefs, SEMrush,
  Anthropic/OpenAI) — those integrations exist upstream but are deliberately **not**
  carried over until ATOSA has the accounts, access, and a real data contract.

## Tools

### `audit-tools/cro-audit/cro_audit.py`

Fetches a URL (no headless browser) and scores it across 8 conversion dimensions:
headline clarity, CTA visibility, social proof, urgency, trust signals, form friction,
mobile responsiveness, page-speed indicators. Pure stdlib + `requests` + `beautifulsoup4`
— no API key, no telemetry (telemetry wrapper from upstream was stripped).

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install -r audit-tools/cro-audit/requirements.txt
python audit-tools/cro-audit/cro_audit.py --url https://atosa.asia --industry saas --json
```

**Verified real result (2026-10-31):** `atosa.asia` scored **64.1/100 (C)**. Top gaps:
missing `<h1>`, weak quantified social proof despite 54 logo elements present, 304KB HTML
with 176 images. Full run log in `audit-tools/cro-audit/TEST-LOG.md`.

### `growth-frameworks/marketing-audit-rubric.md`

A 6-dimension weighted checklist (Content & Messaging 25%, Conversion 20%, SEO 20%,
Competitive Positioning 15%, Brand & Trust 10%, Growth & Strategy 10%) adapted from
`zubair-trabzada/ai-marketing-claude`'s methodology — **the rubric only, not the code**.
Use it manually or as an LLM-agent prompt scaffold; the weights are a heuristic opinion,
not derived from ATOSA data, and must not be presented to Founder/Sales as validated ROI.

## Rules

- No API keys, credentials, or secrets are ever committed. Use `[REDACTED]` as the
  placeholder if an example needs to show a variable name.
- No telemetry: any upstream script that phones home on startup had that code stripped
  before being copied here.
- Every score/output from these tools is a **hypothesis input**, not a conclusion.
  Validate against real ATOSA conversion/revenue data before acting on it at scale.
