# Aqtos for Claude

Work in your company's Aqtos workspace from Claude: look up tasks, projects, clients, leads, invoices, expenses,
employees and calendar events, and create or update them, as yourself and with your own Aqtos permissions.

## What's in the plugin

- **The Aqtos MCP server connection**: points Claude at your workspace's Aqtos server,
  `https://<workspace>.aqtos.io/api/mcp`. When you enable the plugin, Claude asks for your workspace, the part
  before `.aqtos.io` in the address you sign in at (e.g. `yourcompany` for `yourcompany.aqtos.io`).
- **The `aqtos` skill**: instructions that help Claude use the Aqtos tools well, such as checking a record type's
  fields before querying it, turning names into IDs, and confirming with you before anything is deleted.

The plugin runs no scripts or local programs and installs no packages.

## Connecting

1. Enable the plugin and enter your workspace.
2. Claude opens the Aqtos consent page. Sign in with your Aqtos account if asked, check the app name, and select
   **Allow**.

## What it sends and where

Everything goes only to your own workspace's Aqtos server, over HTTPS, signed in through Aqtos OAuth:

- Each tool call sends the tool's inputs (for example a search name, query filters, or the fields of a task to
  create) and receives the results Aqtos returns.
- Aqtos returns only data your account can read and runs only actions your account may take. Your conversation
  with Claude isn't sent beyond what a tool call contains.
- Sign-in uses OAuth 2.1 with PKCE. Aqtos stores the grant as hashed tokens; access tokens last an hour and are
  refreshed while the connection is in use.

You can see and revoke Claude's access at any time in Aqtos: open your profile, go to **Connected apps**, and
select **Revoke**. Privacy policy: https://aqtos.com/privacy/

## Support

https://aqtos.com/contact/

## License

MIT, see [LICENSE](LICENSE). The Aqtos name and logo are not covered by the license.
