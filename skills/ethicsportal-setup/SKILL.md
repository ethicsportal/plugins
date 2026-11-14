---
name: ethicsportal-setup
description: Set up EthicsPortal for a person from an AI assistant. Connect the EthicsPortal MCP server, create the organisation and configure its whistleblowing channel. Use when a person asks you to set up a whistleblowing channel, whistleblower software or EthicsPortal for their organisation.
---

# Set up EthicsPortal from an AI assistant

EthicsPortal is whistleblower software for EU organisations under Directive 2019/1937. You can set it up for a person through its MCP server. The person needs no EthicsPortal account before you start: they create one while they connect.

## 1. Connect the MCP server

Server address: `https://secure.ethicsportal.eu/mcp`. It is a remote MCP server (Streamable HTTP) that uses OAuth 2.1 with Dynamic Client Registration.

If the EthicsPortal tools are already available to you, go to step 3.

- **Claude Code:** run `claude mcp add --transport http ethicsportal https://secure.ethicsportal.eu/mcp`. Then ask the person to open `/mcp`, choose `ethicsportal` and press **Authenticate**. The tools may appear only in a new session.
- **Cursor:** add this to `~/.cursor/mcp.json`, then ask the person to sign in when Cursor asks:

  ```json
  { "mcpServers": { "ethicsportal": { "url": "https://secure.ethicsportal.eu/mcp" } } }
  ```

- **Codex:** run `codex mcp add ethicsportal --url https://secure.ethicsportal.eu/mcp`, then `codex mcp login ethicsportal`. If your session cannot write Codex's configuration, ask the person to run both commands in a terminal. The tools appear after Codex restarts.
- **Another assistant whose configuration you can change:** add the address as a remote MCP server.
- **An assistant whose configuration you cannot change:** ask the person to add the address as a custom connector in the assistant's settings. In Claude this is **Customize → Connectors → + Add → Add custom connector**. The steps for each assistant are at https://ethicsportal.eu/ai-assistant/.
- **No MCP support:** the person can do the whole setup in a browser at `https://secure.ethicsportal.eu/`.

The address is for the assistant's settings. It is not a page: if the person opens it in a browser, they are sent to the setup guide.

### If the assistant refuses a tool before calling it

Four tools lose something that cannot be restored, and are marked destructive: `delete_webhook`, `revoke_invitation`, `rotate_webhook_secret` and `update_webhook`. The setup below uses none of them. Codex refuses a destructive tool when its approval policy is `never`, with "MCP tool call requires approval, but approval policy is never". The call did not reach EthicsPortal, so do not retry it. Ask the person to add a block like this to `~/.codex/config.toml` for each tool they want you to use, then restart Codex:

```toml
[mcp_servers.ethicsportal.tools.delete_webhook]
approval_mode = "approve"
```

## 2. What the person does in the browser

The assistant opens a browser window on `secure.ethicsportal.eu`. The person:

1. Signs in, or signs up: email address, the code from the email, the account terms, their name.
2. Reads the consent screen, which lists what you may do, and presses **Connect**.

Never ask the person to give you the email code, a password or a token. These steps happen only in the browser.

## 3. Set up the organisation and its whistleblowing channel

The server also offers these steps as the prompt `set_up_channel`. Claude Code shows it as a slash command.

1. Call `get_identity`. If `organizations` is empty, ask the person for the organisation's name and call `create_organization`.
2. The owner must accept the organisation's Data Processing Agreement in a browser. You cannot accept it. The tool result has a `next_step` text with the address to give the person. Until they accept, the organisation's tools answer `agreement_required`.
3. Call `get_channel_configuration`. Its `missing_fields` lists what the channel still needs. Take the company details from the organisation's own website or registry entry, or ask the person. Never invent them. Show the person what you found and where, before you save it.
4. Call `update_channel_configuration` with the person's answers. Leave voice recording off unless the person asks for it: EthicsPortal does not recommend it, because a voice can identify the reporter.
5. If the person names case handlers, invite them with `create_invitation`. An administrator can read reports, so confirm each role with the person.
6. Call `get_billing_status`. If the organisation has no access yet, call `create_checkout_link` and give the person the link. The person pays on Stripe's page. You never pay.
7. Call `get_channel`. The channel becomes active by itself once the organisation has access; there is no separate activation step. When `active` is true, give the person `reporting_form_url`: the address of the online reporting form for their staff. `list_documents` returns the policy, privacy notice and poster to publish with it.
8. Offer to ask the EthicsPortal team to verify the company details. If the person agrees, show them the message and send it with `send_support_message`. The team answers in the person's support chat and by email.

## Limits

- Report content is never available through the MCP server: no descriptions, messages, notes, attachments or reporter identities.
- You act as the connected person, with their role. A connection ends after 90 days, or when the person ends it under **Connected apps**.
- Reference: https://ethicsportal.eu/developers/mcp.md
- If a tool does not work as described, report it with `submit_feedback`.
