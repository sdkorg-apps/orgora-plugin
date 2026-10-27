# Ready for Anthropic directory — CoS checklist

Submit from Nik’s Claude (paid) developer portal. Do **not** invent API keys
or submit from an agent session. Keep the public MCP URL stable for Grok.

## Connector (custom or directory)

| Check | Status |
| --- | --- |
| Public HTTPS MCP URL | `https://orgora-mcp.sdkaihub.com/mcp` |
| OAuth discovery (RFC 9728 PRM + AS metadata) | Live |
| Dynamic client registration | Live; accepts `https://claude.ai/api/mcp/auth_callback` |
| PKCE S256 | Live |
| Display name | **Orgora Charts** |
| Support email | support@sdkaihub.com |
| Logo | https://sdkaihub.com/assets/orgora_mark.png (also `assets/orgora_mark.png` in this repo) |
| Privacy | https://sdkaihub.com/orgora-charts/privacy.html |
| Does not break Grok Bot listing | Same URL / OAuth |

## Plugin package (this repo)

| Check | Status |
| --- | --- |
| `.claude-plugin/plugin.json` | `name: orgora`, `displayName: Orgora Charts`, v1.4.0 |
| README ≥ 40 words (what / how / data) | Yes |
| LICENSE | MIT |
| Skill `skills/orgora/SKILL.md` | Workflows for Connect, list-before-create, logos, LinkedIn CDN, use cases, engagement |
| `.mcp.json` cloud entry | `"type": "http"`, same MCP URL |
| No secrets in plugin files | Yes |
| No top-level `bin/` (blocks claude.ai install) | Yes |

## How Nik connects before directory approval

1. Customize → Connectors → Add custom → Web  
2. Name **Orgora Charts**, URL `https://orgora-mcp.sdkaihub.com/mcp`  
3. Connect with Google or Microsoft (same as Orgora app)  
4. Optional: upload/install this plugin for the skill  

## After merge

1. Zip `orgora-plugin` (or point the portal at `sdkorg-apps/orgora-plugin`).  
2. Portal **Validate**, then submit plugin and/or MCP connector.  
3. Worker redeploy **not required** for packaging-only changes. Redeploy
   `tools/orgora-mcp-cloud` only if a companion `orgora_charts` PR changes
   Worker copy/instructions.

## Known blocker (document, do not block submit)

Claude expects OAuth refresh; Orgora MCP access tokens last ~1h and refresh
is intentionally rejected (upstream Drive tokens from GIS/MSAL are not
refreshable in this Connect flow). Users reconnect via Connect when tools
return unauthorized. Fixing refresh is a separate Worker/auth project and
must not break the Grok listing.
