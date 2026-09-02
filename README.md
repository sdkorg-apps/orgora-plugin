# Orgora Charts — Grok plugin

Private org charts for **sales, marketing, consultants, and managers**. This plugin lets [Grok Build](https://x.ai/cli) read the charts on your Mac and, if your Grok session also has an inbox MCP, build a chart from mail.

Charts stay on your device. Orgora never takes Gmail or Outlook passwords through this plugin.

Homepage: [sdkaihub.com/orgora-charts](https://sdkaihub.com/orgora-charts/)

## What you need

1. **Orgora Charts** on a Mac ([App Store](https://apps.apple.com/app/id6792418699) / TestFlight).
2. In Orgora: **More → MCP connector → Enable connector**. That installs the local helper at `~/.orgora/orgora-mcp`. No Terminal.
3. Install this plugin in Grok Build, then **trust** it so the MCP server can start.

```bash
grok plugin install sdkorg-apps/orgora-plugin --trust
```

Once it is listed in the official xAI marketplace:

```bash
grok plugin install orgora --trust
```

Quit and reopen Grok after Enable. Then ask:

> From my inbox MCP, create an Orgora chart for Acme and add the people I have emailed there.

Pair an inbox MCP in the **same** Grok session if you want mail → chart. This plugin does not sign into mail.

## What Grok can do

| Tool | Role |
| --- | --- |
| `list_charts` / `get_chart` / `search_people` / `get_person` / `org_summary` | Read charts on this Mac |
| `create_chart` / `add_person` | Build a chart from mail |
| `update_person_contact_fields` | Fill blank title / phone / company only |

Free-tier caps in Orgora still apply (1 chart, 30 people) unless Orgora Pro is on.

## Security

- **Local stdio only.** The helper is `~/.orgora/orgora-mcp`, installed by the Mac app. This plugin does not download or run a remote binary.
- **No network** from the Orgora helper. It only reads/writes Orgora's chart files.
- **No credentials.** Mail access, if you use it, is a separate MCP you enable in Grok.
- **Fill-only** on existing contact fields; nothing is deleted.

## macOS only

Desktop Grok spawns a local helper. iPhone, grok.com, ChatGPT, and Gemini in a browser cannot use this connector.

## License

MIT. Orgora Charts the app is a product of SDK AI Hub (SDK Equipment LLC).
