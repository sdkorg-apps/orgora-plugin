---
name: orgora
description: >
  Work with Orgora Charts — private org charts for sales, marketing,
  consultants, and managers. Use when the user mentions Orgora, Orgora
  Charts, mapping a buying committee or client org, screenshots of orgs,
  LinkedIn names into Orgora, company logos, headshots, or runs /orgora.
  Prefer the cloud Orgora MCP on Grok Bot so iPhone, Mac, and web stay in
  sync; use the Mac local MCP only for Grok Build / desktop when cloud is
  unavailable. On first turn, open Connect and offer a chart from people
  they have spoken with.
---

# Orgora Charts

Charts live in the user’s **Google Drive or OneDrive** (signed-in Orgora
account) or, on Mac desktop only, local chart files. Do not invent people,
titles, reporting lines, or LinkedIn URLs.

## Template opening (do this first)

On the first Orgora turn in a chat — or whenever a cloud tool returns
unauthorized / Connect missing:

1. **Open the Orgora Connect authentication card** immediately (same Google
   or Microsoft account as the Orgora app). Do not ask for passwords or
   paste tokens into chat. Say briefly that Connect unlocks charts on
   iPhone, Mac, and web.
2. After Connect succeeds (or if already connected), ask once:

   > Want me to make an org chart from people you’ve spoken with
   > (mail / calendar contacts Grok can already see)?

3. If **yes** and a mail/inbox connector is available: run the mail → chart
   flow below, then enrich logo + LinkedIn + photos.
4. If **no** / no mail: ask which company or screenshot to start from.

## Which MCP

1. **Grok Bot / cloud** — `orgora-charts-cloud` at
   `https://orgora-mcp.sdkaihub.com/mcp` after the user Connects Orgora
   (Google or Microsoft). Writes appear on iPhone, Mac, and web.
2. **Mac local (Grok Build)** — `orgora-charts` stdio helper at
   `~/.orgora/orgora-mcp` after **Orgora Charts → Account → MCP connector →
   Enable**. If missing, tell them to Enable; do not download binaries.

## If cloud MCP is missing on Grok Bot

Open / surface the **Orgora Connect** card again. Same Google or Microsoft
as Orgora. Do not ask for passwords or paste tokens into chat.

## Read a chart

1. `list_charts`
2. `get_chart` / `search_people` / `get_person` / `org_summary` as available

## Build or update from mail (“spoken with”)

Only when an **inbox / mail connector** Grok already allows is connected.
Orgora does not sign into mail.

1. `list_charts` — reuse a chart when it exists
2. `create_chart` if needed (`source_note` required; free tier: 3 charts)
3. `add_person` with name + email from mail; `manager_email` if known;
   `source_note` like `inbox, from:jane@acme.com`
4. `update_person_contact_fields` only for **blank** title / phone / company
5. **Company logo:** derive the domain from the company / email hosts →
   `set_chart_logo` with that website
6. **LinkedIn + photo:** for each key person, follow Name → LinkedIn → photo

## Company logo

1. Infer website from company name or email domain (e.g. `jane@acme.com` →
   `acme.com`)
2. Confirm with the user if ambiguous
3. `set_chart_logo` with `website` + `source_note` — the server pulls the
   favicon / apple-touch-icon from that site (not Clearbit)
4. If lookup fails, ask for the correct website or skip

## Name → LinkedIn → photo

1. Search publicly (web / X) only for a **plausible profile URL**
2. Show the candidate to the user and wait for confirmation
3. `set_person_linkedin` (or `add_person` with `linkedin_url`) only after
   they confirm — never invent `/in/` URLs, never scrape LinkedIn HTML
4. If a **public headshot image URL** is already available from search, or
   the user shared a photo in chat, call `set_person_photo` with
   `image_url` or `image_base64` and `source_note`
5. Photos are fill-only unless the user asks to replace (`replace=true`)

## Build from a screenshot in this chat

1. Read the image (org chart, slide, badge, email header)
2. Propose people and reporting lines; ask once if ambiguous
3. `create_chart` / `add_person` with `source_note` like
   `screenshot in Grok chat`
4. Do not invent emails or LinkedIn URLs from a blurry image
5. Offer logo + LinkedIn enrichment after the skeleton is saved

## Rules

- Always pass `source_note` on writes
- Prefer `search_people` before adding (avoid duplicates)
- Free caps: 3 charts, 30 people/chart (unless Pro entitlement is on Drive)
- Do not overwrite filled contact fields; do not delete people or charts
- Tell the user to refresh Orgora on iPhone / Mac / web after cloud writes
- Lead with Connect + the “spoken with” question — that is the product
  template for this bot
