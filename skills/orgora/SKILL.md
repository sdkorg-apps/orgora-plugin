---
name: orgora
description: >
  Work with Orgora Charts — private org charts for sales, marketing,
  consultants, and managers. Use when the user mentions Orgora, Orgora
  Charts, mapping a buying committee or client org, screenshots of orgs,
  LinkedIn names into Orgora, company logos, headshots, or runs /orgora.
  Prefer the cloud Orgora MCP (Claude connector / Grok Bot / Cursor) so
  iPhone, Mac, and web stay in sync; use the Mac local MCP only on desktop
  when cloud is unavailable. On first turn, open Connect and offer a chart
  from people they have spoken with.
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
   > (mail contacts this chat can already see)?

3. If **yes** and a mail/inbox connector is available:
   - Ask which **company** (or primary email domain) this chart is for.
   - Run the mail → chart flow below **scoped to that company** (reuse or
     create only that company’s chart; filter contacts by that domain /
     company name). Then enrich logo + LinkedIn + photos for that chart.
4. If **no**, or no mail connector: ask which company or screenshot to
   start from. Do not promise calendar-only spoken-with unless a calendar
   connector is actually available and you will use it.

## Which MCP

1. **Cloud (preferred)** — `orgora-charts-cloud` at
   `https://orgora-mcp.sdkaihub.com/mcp` after the user Connects Orgora
   (Google or Microsoft). Same URL for Claude custom connectors, Claude
   plugins, Grok Bot, and Cursor. Writes appear on iPhone, Mac, and web.
2. **Mac local** — `orgora-charts` stdio helper at `~/.orgora/orgora-mcp`
   after **Orgora Charts → Account → MCP connector → Enable** (Claude
   Desktop / Grok Build / Cursor desktop). If missing, tell them to Enable;
   do not download binaries. Claude.ai and Cowork use the cloud URL only.

## If cloud MCP is missing or unauthorized

Open / surface the **Orgora Connect** card again (or ask them to Connect
the Orgora Charts connector in Claude Customize → Connectors). Same Google
or Microsoft as Orgora. Do not ask for passwords or paste tokens into chat.

## Read a chart

1. `list_charts`
2. `get_chart` / `search_people` / `get_person` / `org_summary` as available

## Build or update from mail (“spoken with”)

Only when an **inbox / mail connector** this host already allows is connected.
Orgora does not sign into mail. Always pin a **company / domain** first so
contacts and `set_chart_logo` do not land on an unrelated chart.

1. Confirm company name or email domain (e.g. Acme / `acme.com`)
2. `list_charts` — reuse a chart only when it matches that company; otherwise
   `create_chart` (`source_note` required; free tier: 3 charts)
3. From mail, take contacts whose email domain or company matches; skip others
4. `add_person` with name + email; `manager_email` if known;
   `source_note` like `inbox, from:jane@acme.com`
5. `update_person_contact_fields` only for **blank** title / phone / company
6. **Company logo:** only if `get_chart` shows no `logoFile` / company
   website yet (or the user asks to replace) — then `set_chart_logo` for
   that confirmed domain. Never overwrite an existing logo on a reused chart
   without asking.
7. **LinkedIn + photo:** for each key person, follow Name → LinkedIn → photo
8. **Lead status (Warm/Hot/Cold):** after people are on the chart, refresh
   engagement from mail (see below)

## Meeting / Granola touch-point notes

When the user (or Granola / meeting notes) has **multiple touch points**
with different dates for someone on a chart:

1. Find the person (`search_people` / `get_chart`)
2. Call **`append_person_notes`** — one entry per date (prefer
   `occurred_at` + short `note`, or `entries[{text, occurred_at}]`)
3. Always pass `source_note` (e.g. `Granola notes 2026-10-27`)

The server stores each touch point as its own bullet line
(`- YYYY-MM-DD: …`) so notes stay scannable. Do **not** smash several
dates into one paragraph on `add_person.notes`. Do not use
`update_person_contact_fields` for notes (it cannot change notes).

## Use cases from email / Granola (pain · outcome · people · docs)

When mail, Granola, or meeting notes describe a **problem the org is
trying to solve** (not just a touch point on one person):

1. Pin the chart (`list_charts` / company scope)
2. Find the right people (`search_people`) — who owns the pain, who cares
   about the outcome
3. Call **`create_use_case`** with:
   - `name` — short label
   - `pain` — what hurts today, in **plain words a 10-year-old could
     understand** (real pain; no jargon or buzzwords)
   - `outcome` — what better looks like, same plain language (a **real
     positive result**, not vague “efficiency”)
   - `person_ids` — tag those people
   - `document_links` — optional `[{url, label?}]` https links the user
     already has in **Google Drive / OneDrive / SharePoint** (proposal,
     quote, SOW, deck). Orgora stores the **link only** — never upload
     files. Ask for the share link if they mention a doc but do not paste
     one.
   - `source_note` (e.g. `Granola notes 2026-10-27` or
     `inbox thread with jane@acme.com`)
4. Still put chronological meeting bullets on people via
   `append_person_notes` — use cases are the shared problem story; notes
   are the timeline.
5. Refine later with **`update_use_case`** (`people_mode` /
   `document_links_mode`: `add` or `set`). Read with `list_use_cases`.

Do not invent Drive URLs. Do not invent people. Prefer one clear use case
over a wall of vague themes.

## Lead status / engagement (Warm · Hot · Cold)

Lead badges come from `last_email_at` + `email_count` (same rules as the
device mailbox sync). Manual overrides in the app are left alone — never
set `leadStatusOverride` via MCP.

**On demand** (user asks to refresh lead status / warmth, or after you
notice newer mail for someone on the chart):

1. From the mail connector, for matching people note latest message time
   and optional absolute message count
2. One person: `record_person_engagement` with `person_id`,
   `last_email_at` and/or `email_count`, and `source_note`
3. Many people: `refresh_chart_engagement` with `activities`
   (`person_id` or `email` + `last_email_at` / `email_count`) and
   `source_note`

**Periodic / turn-scoped poll** (no Worker cron — do this in the turn):

- When the user opens an Orgora chart conversation, or asks “who’s warm”,
  and a mail connector is available: `get_chart` / `search_people`, then
  `refresh_chart_engagement` for people with emails using the latest
  connector activity. Cap to the active chart / company domain.
- Engagement is **forward-only**: newer dates and higher counts win;
  older polls never regress Warm/Hot/Cold.
- Tell the user to refresh Orgora on iPhone / Mac / web after writes

## Company logo

1. Infer website from company name or email domain (e.g. `jane@acme.com` →
   `acme.com`)
2. Confirm with the user if ambiguous
3. Skip when the chart already has a logo/website unless they ask to replace
4. Otherwise `set_chart_logo` with `website` + `source_note` — the server
   pulls the favicon / apple-touch-icon from that site (not Clearbit)
5. If lookup fails, ask for the correct website or skip

## Name → LinkedIn → photo

1. Search publicly (web / X) only for a **plausible profile URL**
2. While searching, also capture any **`media.licdn.com` headshot URL**
   shown for that profile (search result thumbnail / og:image). Keep it.
3. Show the candidate `/in/` URL to the user and wait for confirmation
4. After confirm, call `set_person_linkedin` (or `add_person`) with:
   - `linkedin_url` (confirmed)
   - **`image_url` = that `media.licdn.com` URL when you have one**
   - `source_note`
   Never invent `/in/` URLs. LinkedIn URL is fill-only unless
   `replace=true`.
5. **Important:** the cloud Worker usually **cannot** scrape LinkedIn
   profile HTML (LinkedIn returns HTTP 999 → `photo.status=blocked`).
   CDN image download **does** work. Do **not** stop after saving only
   the `/in/` URL — always attach the photo via `image_url` when search
   showed one.
6. If `photo.status` is `blocked`, `authwalled`, `no_photo`, or `failed`:
   - Immediately `set_person_photo` with the `media.licdn.com` `image_url`
     from search (or `image_base64` if the user pasted a photo)
   - Else ask for a headshot in chat, or tell them to use the Orgora
     Clipper while signed in to LinkedIn
7. Photos are fill-only unless the user asks to replace (`replace=true`)

## Build from a screenshot in this chat

1. Read the image (org chart, slide, badge, email header)
2. Propose people and reporting lines; ask once if ambiguous
3. `create_chart` / `add_person` with `source_note` like
   `screenshot in chat`
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
