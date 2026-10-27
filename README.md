# Orgora Charts — Claude + Grok plugin

Private org charts for **sales, marketing, consultants, and managers**.

Works with:

- **Claude** (claude.ai, Cowork, Claude Desktop, Claude Code, Duet) — add the
  remote MCP as a **custom connector**, or install this **plugin** so skills +
  connector ship together. Charts write into the user’s Drive / OneDrive.
- **Grok Bot** — Connect Orgora (Google or Microsoft). Same hosted MCP.
- **Mac desktop** — local helper from the Mac app (`~/.orgora/orgora-mcp`)
  when cloud is unavailable.

Orgora never takes Gmail or Outlook passwords through this plugin. Mail and
screenshots come from the host’s own connectors / vision.

Homepage: [sdkaihub.com/orgora-charts](https://sdkaihub.com/orgora-charts/)  
Support: [support@sdkaihub.com](mailto:support@sdkaihub.com)  
Logo: [sdkaihub.com/assets/orgora_mark.png](https://sdkaihub.com/assets/orgora_mark.png)

## Connect Claude (custom connector)

Anyone on a paid Claude plan can connect without waiting for directory review:

1. Open **Customize → Connectors → Add → Custom** (Owners add for Team /
   Enterprise).
2. Name: **Orgora Charts**
3. URL: `https://orgora-mcp.sdkaihub.com/mcp`
4. Keep the detected OAuth settings (Sign in when needed; Claude’s published
   identity or automatic registration both work — DCR is enabled).
5. Finish **Add**, then **Connect** and sign in with the same Google or
   Microsoft account as the Orgora app.
6. Enable the connector on the chat **+ → Connectors** menu when you need it.

After Connect, ask Claude to `list_charts` before creating anything.

Claude Duet has no separate marketplace — it uses the same account connectors
and plugins, so the steps above (or installing this plugin) cover Duet too.

## Install this plugin (skills + MCP)

This repo is the Anthropic-style plugin package:

| Path | Role |
| --- | --- |
| `.claude-plugin/plugin.json` | Anthropic directory / Claude Code manifest |
| `.mcp.json` | Remote HTTP MCP + optional Mac stdio |
| `skills/orgora/SKILL.md` | Chart workflows (Connect, list before create, logos, LinkedIn CDN photos, use cases, engagement) |
| `plugin.json` / `.grok-plugin/` | Grok Bot marketplace packaging (unchanged URL) |

### Claude Code

```bash
claude --plugin-dir /path/to/orgora-plugin
# or after publishing to a marketplace / directory:
# claude plugin install orgora@…
```

### claude.ai / Cowork

Zip this folder (manifest at `.claude-plugin/plugin.json`) and upload under
**Customize → Plugins → Add → Upload plugin**, or submit via Anthropic’s
developer portal (CoS / Founder account — do not invent credentials here).

### Grok Bot

```bash
grok plugin install sdkorg-apps/orgora-plugin --trust
```

## Template opening (recommended)

1. **Connect first** — Orgora Connect / Claude connector (same Google or
   Microsoft as the app) so tools can write to Drive/OneDrive.
2. Ask: *Want me to make an org chart from people you’ve spoken with?*
3. If yes (and mail is available): ask which **company / domain**,
   `list_charts` then reuse or create only that chart, filter contacts,
   set the **company logo** only when the chart has none yet, confirm
   **LinkedIn** URLs, attach **headshots** via `media.licdn.com` `image_url`
   from search, and refresh **Warm/Hot/Cold** with
   `record_person_engagement` / `refresh_chart_engagement`.
4. Charts show on iPhone, Mac, and web after refresh.

## Tools

| Tool | Role |
| --- | --- |
| `list_charts` / `get_chart` / `search_people` | Read |
| `create_chart` / `add_person` | Create (requires `source_note`) |
| `update_person_contact_fields` | Fill blank title / phone / company only |
| `list_use_cases` / `create_use_case` / `update_use_case` | Pain / outcome / people / Drive links |
| `append_person_notes` | Granola / touch-point bullet lines |
| `set_chart_logo` | Fetch logo from company website |
| `set_person_linkedin` | Confirmed `/in/` URL; pass `image_url` (`media.licdn.com`) |
| `set_person_photo` | Public image URL or base64 headshot |
| `record_person_engagement` / `refresh_chart_engagement` | Warm/Hot/Cold |

Free-tier caps still apply (3 charts, 30 people) unless Pro is on for that
account.

## Security

- **Cloud path:** Connect issues an MCP token; the Worker uses the user’s
  Drive/OneDrive grant. Chart files stay in the user’s cloud.
- **Local path:** stdio helper, no network, charts on this Mac.
- **No mail credentials** in Orgora MCP.
- **LinkedIn:** only a user-confirmed `/in/` URL. Prefer CDN `image_url`
  for photos (HTML scrape from Workers often HTTP 999).
- **Fill-only** on existing contact fields; nothing is deleted.

## Known Claude caveat

MCP access tokens expire after about one hour and the Worker intentionally
does not issue refresh tokens (upstream Google GIS / MSAL browser tokens are
short-lived). If tools return unauthorized, Connect again. Do not paste
tokens into chat.

## Ready for Anthropic directory (CoS checklist)

See [docs/ANTHROPIC_DIRECTORY.md](docs/ANTHROPIC_DIRECTORY.md).

## License

MIT. Orgora Charts is a product of SDK AI Hub (SDK Equipment LLC).
