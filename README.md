# Playbook plugins

A plugin marketplace for [Playbook](https://www.playbook.com). It works in Claude Code, in Claude on the web and desktop, and in Codex.

## Plugins

### `playbook`

Connects an assistant to your Playbook workspace through the hosted Playbook MCP server (`https://mcp.playbook.com/mcp`), with skills for finding, organizing, uploading, sharing and publishing assets. See [`playbook-mcp/README.md`](./playbook-mcp/README.md) for what it contains and how it handles data.

## Install

Claude Code:

```
claude plugin marketplace add playbook-labs/mcp-plugins
claude plugin install playbook@playbook-plugins
```

Inside a session the same two steps are `/plugin marketplace add playbook-labs/mcp-plugins` and `/plugin install playbook@playbook-plugins`. Then run `/mcp`, pick **playbook-creative**, choose **Authenticate**, and click Allow on the Playbook consent screen.

Claude on the web and the desktop app: open **Customize > Plugins**, choose **Add**, then **Add marketplace**, and enter `playbook-labs/mcp-plugins`.

Codex:

```
codex plugin marketplace add playbook-labs/mcp-plugins
codex plugin add playbook@playbook-plugins
```
