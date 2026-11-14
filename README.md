# EthicsPortal plugin

[EthicsPortal](https://ethicsportal.eu) is whistleblower software for EU organisations under Directive 2019/1937. This plugin connects AI assistants to it.

It adds the EthicsPortal MCP server and three skills. With them, an assistant can create the organisation, configure its whistleblowing channel, invite case handlers, and read report status and deadlines. Report content is never available through the server: no descriptions, messages, notes, attachments or reporter identities.

## Claude Code

```sh
claude plugin marketplace add ethicsportal/plugins
claude plugin install ethicsportal@ethicsportal
```

Then open `/mcp`, choose `ethicsportal` and press **Authenticate**. You sign in, or create an account, in the browser.

## Codex

```sh
codex plugin marketplace add ethicsportal/plugins
codex plugin add ethicsportal@ethicsportal
```

Codex asks you to sign in when the plugin is installed.

## Cursor

Until the plugin is listed in Cursor's marketplace, clone this repository and link it as a local plugin:

```sh
git clone https://github.com/ethicsportal/plugins.git ~/ethicsportal-plugins
ln -s ~/ethicsportal-plugins ~/.cursor/plugins/local/ethicsportal
```

On a Cursor Teams plan, an admin can instead use **Import from Repo** with `https://github.com/ethicsportal/plugins`.

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

## What it connects to

- `https://secure.ethicsportal.eu/mcp`: the MCP server, with OAuth sign-in. A connection acts with your role in each organisation and ends after 90 days, or when you end it under **Connected apps**.
- Pages the skills point to on `ethicsportal.eu`: the developer documentation, `openapi.json`, `llms.txt` and the country pages on whistleblower laws.

The skills are published at `https://ethicsportal.eu/.well-known/agent-skills/` and copied here every week by `bin/sync-skills`, which also raises the plugin version so existing installs update. Change a skill on the website, not here.

## Support

support@ethicsportal.eu · [Developer documentation](https://ethicsportal.eu/developers/) · [Privacy](https://ethicsportal.eu/privacy/)
