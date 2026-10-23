---
name: orgora
description: >
  Work with Orgora Charts — private org charts for sales, marketing,
  consultants, and managers. Use when the user mentions Orgora, Orgora
  Charts, mapping a buying committee or client org, screenshots of orgs,
  LinkedIn names into Orgora, or runs /orgora. Prefer the cloud Orgora MCP
  on Grok Bot so iPhone, Mac, and web stay in sync; use the Mac local MCP
  only for Grok Build / desktop when cloud is unavailable.
---

# Orgora Charts

Charts live in the user’s **Google Drive or OneDrive** (signed-in Orgora
account) or, on Mac desktop only, local chart files. Do not invent people,
titles, reporting lines, or LinkedIn URLs.

## Which MCP

1. **Grok Bot / cloud** — `orgora-charts-cloud` at
   `https://orgora-mcp.sdkaihub.com/mcp` after the user Connects Orgora
   (Google or Microsoft). Writes appear on iPhone, Mac, and web.
2. **Mac local (Grok Build)** — `orgora-charts` stdio helper at
   `~/.orgora/orgora-mcp` after **Orgora Charts → Account → MCP connector →
   Enable**. If missing, tell them to Enable; do not download binaries.

## If cloud MCP is missing on Grok Bot

Tell the user to open the Orgora connector card, Connect with the same
Google or Microsoft account they use in Orgora, then retry. Do not ask for
passwords or paste tokens into chat.

## Read a chart

1. `list_charts`
2. `get_chart` / `search_people` / `get_person` / `org_summary` as available

## Build or update from mail

Only when an **inbox / mail connector** Grok already allows is connected.
Orgora does not sign into mail.

1. `list_charts` — reuse a chart when it exists
2. `create_chart` if needed (`source_note` required; free tier: 3 charts)
3. `add_person` with name + email from mail; `manager_email` if known;
   `source_note` like `inbox, from:jane@acme.com`
4. `update_person_contact_fields` only for **blank** title / phone / company

## Build from a screenshot in this chat

1. Read the image (org chart, slide, badge, email header)
2. Propose people and reporting lines; ask once if ambiguous
3. `create_chart` / `add_person` with `source_note` like
   `screenshot in Grok chat`
4. Do not invent emails or LinkedIn URLs from a blurry image

## Name → LinkedIn → Orgora

1. Search publicly (web / X) only for a **plausible profile URL**
2. Show the candidate to the user and wait for confirmation
3. `add_person` with `linkedin_url` only after they confirm
4. Never scrape LinkedIn HTML; never invent `/in/` URLs

## Rules

- Always pass `source_note` on writes
- Prefer `search_people` before adding (avoid duplicates)
- Free caps: 3 charts, 30 people/chart (unless Pro entitlement is on Drive)
- Do not overwrite filled contact fields; do not delete people or charts
- Tell the user to refresh Orgora on iPhone / Mac / web after cloud writes
