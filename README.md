# DocGraph Agent Plugins

DocGraph Agent Plugins package DocGraph for agent/plugin ecosystems. This repository contains a Claude Code Plugin and a Codex Plugin that connect local agents to a running DocGraph HTTP MCP endpoint:

```sh
http://127.0.0.1:8787/mcp
```

DocGraph is a local-first document knowledge graph for enterprise documents and local agents. The plugin does not upload your documents to a SaaS service. Run one local `docgraph serve` process for the workspace, then let Claude Code or Codex connect to that process over MCP Streamable HTTP.

The first release is intentionally simple: install the DocGraph CLI yourself, make sure `docgraph` is on `PATH`, start `docgraph serve`, then install the plugin.

## Repository Layout

```text
docgraph-agent-plugins/
  plugins/docgraph/              # Shared plugin package
    .mcp.json                    # Claude Code and Codex HTTP MCP config
    .mcp.stdio.json              # Optional stdio fallback config
    .codex-plugin/plugin.json    # Codex plugin manifest
    .claude-plugin/plugin.json   # Claude Code plugin manifest
    assets/                      # Icon and DocGraph UI screenshots
  .agents/plugins/marketplace.json
  .claude-plugin/marketplace.json
  docs/
```

## Install DocGraph CLI

Install DocGraph from the main project:

- Main repository: https://github.com/gwork1883/docgraph

After installation, verify that the CLI is available:

```sh
docgraph --help
```

If your shell cannot find `docgraph`, add the CLI install directory to `PATH`, then restart Claude Code or Codex.

## Start DocGraph

Open the workspace where your agent should use DocGraph and start a local server:

```sh
docgraph serve --data .docgraph --host 127.0.0.1 --port 8787
```

The server exposes MCP at `http://127.0.0.1:8787/mcp`.

That keeps the index scoped to the current workspace by default. Use a separate `.docgraph` directory per workspace when you want separate indexes.

## Add Document Sources

Use the DocGraph CLI or DocGraph UI from the main project to add document sources such as local documentation, internal docs, or other supported connectors.

For the first plugin release, source management stays in DocGraph itself. The plugins only provide agent access through MCP.

## Sync Documents

Sync your configured sources in DocGraph before asking an agent to search or reason over them. The local `.docgraph` directory stores the graph and indexes used by MCP tools.

After sync, the MCP server exposes tools including:

- `doc_search`
- `doc_context`
- `doc_get_node`
- `doc_get_section`
- `doc_related`
- `doc_impact`

## Install The Claude Code Plugin

The Claude Code plugin package is in `plugins/docgraph`.

For local development, add this repository as a Claude Code plugin marketplace, then install the `docgraph` plugin from that marketplace. The plugin package includes `.claude-plugin/plugin.json` and a root `.mcp.json` file. The repo-level Claude marketplace file is:

```text
.claude-plugin/marketplace.json
```

TODO: Before publishing, verify the current Claude Plugin Hub submission and marketplace commands in the official Claude Code plugin docs:

- https://docs.anthropic.com/en/docs/claude-code/plugins
- https://docs.anthropic.com/en/docs/claude-code/plugin-marketplaces

## Install The Codex Plugin

The Codex plugin package is also in `plugins/docgraph`.

The repo-level Codex marketplace file is:

```text
.agents/plugins/marketplace.json
```

Install or add this marketplace in Codex according to the current Codex plugin workflow, then enable the `docgraph` plugin. The plugin manifest points Codex to `./.mcp.json`, which connects to `http://127.0.0.1:8787/mcp`.

## Verify MCP Tools

First verify the HTTP MCP endpoint outside the agent host:

```sh
curl -s http://127.0.0.1:8787/api/health
```

Then enable the plugin in Claude Code or Codex and ask the host to list or use DocGraph MCP tools. At minimum, verify that `doc_search` is visible and can search a synced local index.

## FAQ

### Does the plugin upload my documents?

No. The plugin connects to a local DocGraph HTTP MCP endpoint. Your documents and indexes stay in the local `.docgraph` data directory unless you separately configure DocGraph connectors or services that access network sources.

### Does the plugin install DocGraph CLI?

No. The first release does not download, bundle, or update binaries. Install DocGraph CLI separately and put `docgraph` on `PATH`.

### Why does the MCP server fail with `command not found`?

The agent host cannot find `docgraph`. Install DocGraph CLI or fix `PATH`, then restart Claude Code or Codex.

### Which data directory does the plugin use?

By default DocGraph uses `.docgraph` in the workspace where you start `docgraph serve`.

### Can I change the data directory?

Yes, but do it intentionally by starting `docgraph serve` with the desired `--data` directory or config file. The default plugin package connects to `http://127.0.0.1:8787/mcp`.

### Why prefer HTTP MCP over `docgraph mcp --data .docgraph`?

HTTP MCP lets one long-running DocGraph server own the workspace data directory, Web UI, REST API, sync jobs, and MCP endpoint. Starting a separate `docgraph mcp --data .docgraph` process for each agent can make Claude Code, Codex, and the Web UI read or write the same SQLite store concurrently. The stdio config is still included as `.mcp.stdio.json` for isolated fallback testing, but the default plugin path uses HTTP.

## Release Preparation

Use these docs before publishing:

- [Install guide](docs/install.md)
- [Release checklist](docs/release-checklist.md)
- [Plugin Hub submission notes](docs/pluginhub-submission.md)
