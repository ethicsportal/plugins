# EthicsPortal plugins

[EthicsPortal](https://ethicsportal.eu) is whistleblower software for EU organisations under Directive 2019/1937. This repository connects AI assistants to it.

The plugin adds the EthicsPortal MCP server (`https://secure.ethicsportal.eu/mcp`) and three skills. With them, an assistant can create the organisation, configure its whistleblowing channel, invite case handlers, and read report status and deadlines. Report content is never available through the server: no descriptions, messages, notes, attachments or reporter identities.

## Claude Code

```sh
claude plugin marketplace add ethicsportal/plugins
claude plugin install ethicsportal@ethicsportal
```

Then open `/mcp`, choose `ethicsportal` and press **Authenticate**. You sign in, or create an account, in the browser.

## Cursor

`plugins/cursor/ethicsportal` adds the MCP server. Until it is listed in Cursor's marketplace, add this to `~/.cursor/mcp.json`:

```json
{ "mcpServers": { "ethicsportal": { "url": "https://secure.ethicsportal.eu/mcp" } } }
```

## Other assistants

Install the skills with:

```sh
npx skills add ethicsportal/plugins
```

Then add `https://secure.ethicsportal.eu/mcp` as a remote MCP server. The steps for each assistant are at https://ethicsportal.eu/ai-assistant/.

## Skills

| Skill | Use |
| --- | --- |
| `ethicsportal-setup` | Set up EthicsPortal and the whistleblowing channel for a person |
| `ethicsportal-api` | Write code against the EthicsPortal REST API and webhooks |
| `whistleblower-compliance-lookup` | Look up how each EU member state transposed Directive 2019/1937 |

The skills are published at `https://ethicsportal.eu/.well-known/agent-skills/` and copied here by `bin/sync-skills`. Change them there, not here.

## Support

support@ethicsportal.eu · [Developer documentation](https://ethicsportal.eu/developers/)
