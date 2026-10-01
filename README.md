# Aqtos plugins

Plugin package that connects ChatGPT and Claude to Aqtos over MCP. The tools themselves are the Aqtos backend's
`/mcp` endpoint on each workspace; this package only tells each app where that server is, how to present it, and
how to work with it.

```
aqtos-plugins/                          marketplace root
├── .claude-plugin/marketplace.json     Claude Code marketplace
├── .agents/plugins/marketplace.json    ChatGPT / Codex local marketplace
└── plugins/aqtos/
    ├── plugin.json                     ChatGPT: portable manifest + extensions.com.openai.interface
    ├── mcp.json                        ChatGPT: MCP server URL
    ├── .claude-plugin/plugin.json      Claude Code: manifest, asks for the tenant's MCP URL
    ├── .mcp.json                       Claude Code: MCP server, URL from that setting
    ├── skills/aqtos/SKILL.md           Both: how to work in Aqtos through the tools
    ├── README.md, LICENSE              Directory listing text and MIT license (CTRLALTDEL Media Inc.)
    └── assets/                         logo.png (512x512), icon.png (192x192)
```

Every Aqtos tenant is its own deployment, so the MCP URL is `https://<tenant host>/api/mcp`. Signing in uses Aqtos
OAuth: the app opens the Aqtos consent page, the user signs in and clicks Allow, and the tools then run as that
user with their permissions.

## Claude Code

```
claude plugin marketplace add Aqtos/aqtos-plugins    # or a local checkout's path
claude plugin install aqtos@aqtos
```

When the plugin is enabled, Claude Code asks for **Your Aqtos workspace**, e.g. `iwm` for `iwm.aqtos.io`; the
MCP URL is `https://<workspace>.aqtos.io/api/mcp`, the same template as the Claude directory connector listing. Then
run `/mcp`, pick `aqtos` and authenticate: the browser opens the Aqtos consent page.

Validate after edits, from the repo root: `claude plugin validate .` and `claude plugin validate ./plugins/aqtos`

## Claude.ai / Claude Desktop

No package needed: Settings → Connectors → Add custom connector, URL `https://<tenant host>/api/mcp`.

## ChatGPT

`mcp.json` points at dev (`https://dev.aqtos.io/api/mcp`); change it for another tenant.

1. ChatGPT Settings → Security and login → enable **Developer Mode**.
2. Plugins → **+** → add the MCP server URL, authentication OAuth. ChatGPT registers itself with Aqtos and opens the
   Aqtos consent page.
3. Copy the connection id from the browser URL (`plugin_asdk_app_…`) and give it to `@plugin-creator` together with
   this plugin. It writes `.app.json`; add `"apps": "./.app.json"` under `extensions.com.openai` in `plugin.json`.
4. Test in a new chat, e.g. "What are my open tasks in Aqtos this week?".

Local marketplace for the ChatGPT desktop app: copy `.agents/plugins/marketplace.json` to
`~/.agents/plugins/marketplace.json`, copy `plugins/aqtos` to `~/.codex/plugins/aqtos`, restart the app.

## Before a public listing

- One public URL for all tenants: ChatGPT's directory takes a single production MCP URL, and today each tenant has
  its own.
- Verified publisher identity and domain verification on the OpenAI Platform; reviewer demo credentials without MFA.
- Screenshots in `assets/` and `screenshots` in `plugin.json`.
