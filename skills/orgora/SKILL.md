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
   > (mail contacts Grok can already see)?

3. If **yes** and a mail/inbox connector is available:
   - Ask which **company** (or primary email domain) this chart is for.
   - Run the mail → chart flow below **scoped to that company** (reuse or
     create only that company’s chart; filter contacts by that domain /
     company name). Then enrich logo + LinkedIn + photos for that chart.
4. If **no**, or no mail connector: ask which company or screenshot to
   start from. Do not promise calendar-only spoken-with unless a calendar
   connector is actually available and you will use it.

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
2. Show the candidate to the user and wait for confirmation
3. `set_person_linkedin` (or `add_person` with `linkedin_url`) only after
   they confirm — never invent `/in/` URLs. LinkedIn URL is fill-only
   unless the user asks to replace (`replace=true`).
4. Leave `fetch_photo` at its default (**true**). The cloud Worker fetches
   the **public** profile page for JSON-LD / `og:image` (`media.licdn.com`)
   and attaches the headshot when LinkedIn exposes one without login —
   same idea as in-app Refresh.
5. If the tool returns `photo.status` of `authwalled` or `no_photo`, ask
   the user for a headshot in chat and call `set_person_photo` with
   `image_url` / `image_base64`, or tell them to use the Orgora Clipper
   while signed in to LinkedIn. A known `media.licdn.com` URL from search
   may also go straight to `set_person_photo`.
6. Photos are fill-only unless the user asks to replace (`replace=true`)

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
