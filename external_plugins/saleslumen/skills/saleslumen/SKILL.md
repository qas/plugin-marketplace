---
name: saleslumen
description: >-
  Overview of Saleslumen MCP for Grok: OAuth linking, whoami, organizations,
  namespaces, and which tools cover Campaigns, Emails, Workflows, and Apps Script.
  Use when the user mentions Saleslumen, saleslumen MCP, or asks to connect
  Grok to Saleslumen.
---

# Saleslumen

Saleslumen products exposed through MCP: **Workflows**, **Emails**, **Campaigns**, and **Apps Script**. This plugin connects Grok to the hosted MCP server at `https://mcp.saleslumenapis.com/`.

## Before any tool call

1. Ensure the Saleslumen MCP server is linked (OAuth). If tools return unauthorized, re-link.
2. Call `whoami` for token identity and the OAuth-pinned organization ID (`sub`, `sl_organization_id`, audience, scope). It does not return an organization name.
3. Call `list_my_organizations` for named active memberships `[{id, name}]`. Name the current organization by matching `whoami.sl_organization_id` against that list. Reconnect to switch the OAuth organization.
4. Call `list_my_namespaces` for namespaces in the OAuth organization as `[{id, name}]`. Product tools take optional `namespace_id` (UUID or `namespaces/{uuid}`). Omit it for organization scope. Do not pass a namespace name. A token with `sl_namespace_id` is `401`; reconnect for an organization-only token.

Prefer confirming identity with `whoami` before destructive actions (delete, send, publish, activate, cancel).

## Products and tools

| Product | Tool prefix | Use for |
| --- | --- | --- |
| Identity | `whoami`, `list_my_organizations`, `list_my_namespaces` | Auth pin, named memberships, namespace pick |
| Campaigns | `campaigns_*` | Campaigns, people, sequences, deliveries, suppressions, schedules |
| Emails | `emails_*` | Messages, threads, labels, drafts, accounts, discover |
| Workflows | `workflows_*` | Drafts, publish, activate, executions. Publish does not activate. A `version_id` start is preview. |
| Apps Script | `apps_script_*` | Projects, content, versions, deployments, run. Cannot install Marketplace apps. |

Use the specialized skills when the task is product-specific:

- Campaigns / sequences / people → `saleslumen-campaigns`
- Emails (inbox, drafts, send, discover) → `saleslumen-emails`

MCP does not connect mailboxes, verify address lists, install Marketplace apps, buy domains, or manage billing or team access. Do that in Saleslumen first.

## Auth model (what Grok should know)

- MCP tokens are minted for audience `https://mcp.saleslumenapis.com`.
- Required scope is `api:read`.
- Do not ask the user for API keys for this connector; OAuth is the supported path.
- Sign-in uses the user's Saleslumen browser session. Consent is at `oauth.saleslumen.com`. Wrong user → sign out of `app.saleslumen.com`, sign in as the right user, then re-link.

## Safety

- Confirm before send, delete, trash, publish, activate, or cancel.
- Prefer list/get tools to gather IDs before mutating.
- Stay inside the authenticated organization; do not invent org, namespace, or resource IDs.

## Links

- Product: https://www.saleslumen.com
- App: https://app.saleslumen.com
- MCP: https://mcp.saleslumenapis.com/
- OAuth issuer: https://oauth.saleslumenapis.com
