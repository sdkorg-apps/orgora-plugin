---
name: orgora
description: >
  Work with Orgora Charts on this Mac — private org charts for sales,
  marketing, consultants, and managers. Use when the user mentions Orgora,
  Orgora Charts, mapping a buying committee or client org into Orgora, or
  asking Grok to read or populate an Orgora chart from mail. Use when the
  user runs /orgora.
---

# Orgora Charts

Orgora stores org charts **on this Mac**. Use the `orgora-charts` MCP tools. Do not invent people, titles, or reporting lines.

## If the MCP server is missing

Tell the user, on a Mac: open **Orgora Charts → More → MCP connector → Enable connector**, then quit and reopen Grok. Do not download a binary or run install scripts.

## Read a chart

1. `list_charts`
2. `get_chart` / `search_people` / `get_person` / `org_summary` as needed

## Build a chart from mail

Only when an **inbox MCP** is also connected in this session (Gmail, Outlook, …). Orgora does not sign into mail.

1. `list_charts` — reuse a chart if it already exists
2. `create_chart` if needed (free tier: 1 real chart unless Pro)
3. `add_person` with name + email from mail; `manager_email` if known
4. `update_person_contact_fields` only for **blank** title / phone / company
5. Tell the user to reopen the chart in Orgora if it is on screen

Do not overwrite filled fields. Do not delete people. Stamp `source_note` (e.g. `inbox MCP, from:jane@acme.com`).

## Audience

Sales and marketing map accounts and buying committees. Consultants and managers map client orgs and reporting lines.
