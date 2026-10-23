# Orgora Charts — Grok plugin

Private org charts for **sales, marketing, consultants, and managers**.

Works with:

- **Grok Bot** — Connect Orgora (Google or Microsoft). The hosted MCP writes
  charts into **that user’s Drive or OneDrive**. The same charts open on
  **iPhone, Mac, and web**.
- **Grok Build / Mac desktop** — local helper from the Mac app
  (`~/.orgora/orgora-mcp`). Charts stay on the Mac; Drive sync happens when
  the signed-in app uploads.

Orgora never takes Gmail or Outlook passwords through this plugin. Mail and
screenshots come from Grok’s own connectors / vision.

Homepage: [sdkaihub.com/orgora-charts](https://sdkaihub.com/orgora-charts/)

## Grok Bot

1. Install this plugin from the marketplace (or
   `sdkorg-apps/orgora-plugin`).
2. Connect **Orgora** with the same Google or Microsoft account you use in
   the Orgora app.
3. Ask, for example:

> From my mail connector, create an Orgora chart for Acme and add the people
> I’ve emailed there.

> Here’s a screenshot of their org — put these people into Orgora.

> Add Jane Doe to the Acme chart after I confirm her LinkedIn URL.

Hosted MCP: `https://orgora-mcp.sdkaihub.com/mcp` (OAuth Connect —
Google or Microsoft; charts stay in that account’s Drive / OneDrive)

## Grok Build (Mac)

1. Install **Orgora Charts** on a Mac.
2. **Account → MCP connector → Enable connector** (installs
   `~/.orgora/orgora-mcp`).
3. Install and trust this plugin:

```bash
grok plugin install sdkorg-apps/orgora-plugin --trust
```

Once listed in the official catalog:

```bash
grok plugin install orgora --trust
```

Quit and reopen Grok after Enable. Pair an inbox MCP in the same session
for mail → chart.

## Tools

| Tool | Role |
| --- | --- |
| `list_charts` / `get_chart` / `search_people` | Read |
| `create_chart` / `add_person` | Create (requires `source_note`) |
| `update_person_contact_fields` | Fill blank title / phone / company only |

Free-tier caps in Orgora still apply (3 charts, 30 people) unless Pro is on
for that account.

## Security

- **Cloud path:** Connect issues an MCP token; the Worker uses the user’s
  Drive/OneDrive grant for that session. Chart files stay in the user’s
  cloud — Orgora does not host them.
- **Local path:** stdio helper, no network, charts on this Mac.
- **No mail credentials** in Orgora MCP.
- **No LinkedIn scrape** — only a user-confirmed profile URL.
- **Fill-only** on existing contact fields; nothing is deleted.

## License

MIT. Orgora Charts is a product of SDK AI Hub (SDK Equipment LLC).
