# Install DocGraph Agent Plugins

DocGraph Agent Plugins provide Claude Code Plugin and Codex Plugin integration for a local DocGraph workspace. After installation, the plugin connects the agent host to a running DocGraph HTTP MCP endpoint:

```sh
http://127.0.0.1:8787/mcp
```

DocGraph is local-first. Your enterprise documents, indexes, and agent context stay in the local `.docgraph` data directory unless you explicitly configure other behavior in DocGraph itself. The plugins do not upload your documents to a SaaS service.

## Requirements

- DocGraph CLI installed separately and available on `PATH`.
- A local workspace where DocGraph can create or read `.docgraph`.
- Claude Code or Codex, depending on which plugin you install.

The first release does not download or install the DocGraph CLI automatically. Install DocGraph CLI before installing either plugin.

## Verify DocGraph CLI

Run:

```sh
docgraph --help
docgraph serve --data .docgraph --host 127.0.0.1 --port 8787
```

The server should keep running and expose Web/API/MCP on `127.0.0.1:8787`. If the command is not found, add the DocGraph CLI install directory to `PATH`.

## Install the Claude Code Plugin

1. Install DocGraph CLI and confirm `docgraph` is on `PATH`.
2. Install the Claude Code Plugin from the published plugin package or registry entry.
3. Open Claude Code in the workspace that should use DocGraph.
4. Enable the DocGraph plugin.
5. Confirm the plugin MCP URL is configured as:

```sh
http://127.0.0.1:8787/mcp
```

Use one `.docgraph` directory per local workspace when you want separate document indexes. Reuse the same `.docgraph` directory when you want the same indexed enterprise documents available to multiple local agents.

## Install the Codex Plugin

1. Install DocGraph CLI and confirm `docgraph` is on `PATH`.
2. Install the Codex Plugin from the published plugin package or marketplace entry.
3. Enable the DocGraph plugin in Codex.
4. Confirm the plugin MCP URL is configured as:

```sh
http://127.0.0.1:8787/mcp
```

Codex should connect to the already-running DocGraph HTTP MCP endpoint when the plugin is active. The plugin should not require a cloud DocGraph account for local document access.

## Local Test Commands

Run these commands from this repository checkout.

### Claude Code

```sh
docgraph serve --data .docgraph --host 127.0.0.1 --port 8787
claude plugin validate plugins/docgraph
claude plugin marketplace add . --scope user
claude plugin install docgraph@docgraph-agent-plugins --scope user
claude plugin list
```

Start a new Claude Code session from the same shell and ask it to list or use the DocGraph MCP tools. At minimum, verify `doc_search`.

### Codex

```sh
docgraph serve --data .docgraph --host 127.0.0.1 --port 8787
python3 /root/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py plugins/docgraph
codex plugin marketplace add .
codex plugin add docgraph@docgraph-agent-plugins
codex plugin list
```

Start a new Codex thread from the same shell and ask it to list or use the DocGraph MCP tools. At minimum, verify `doc_search`.

## Troubleshooting

### `docgraph` command not found

Install DocGraph CLI first, then make sure its install directory is in `PATH`. Restart Claude Code or Codex after changing `PATH`.

### MCP endpoint is unavailable

Run the server manually from the same workspace:

```sh
docgraph serve --data .docgraph --host 127.0.0.1 --port 8787
```

Check the CLI output for missing permissions, invalid data directory state, occupied ports, or unsupported CLI version.

### No documents appear in the agent

Confirm that the local `.docgraph` directory is the intended data directory and that DocGraph has indexed the enterprise documents you want the local agent to use.

### Data handling expectations

The plugin connects to a local MCP endpoint exposed by `docgraph serve`. Document content and indexes are expected to remain local to the workspace data directory. Review any separate DocGraph connector configuration before connecting network sources.

### Concurrency and data directory ownership

Prefer one long-running `docgraph serve` process per `.docgraph` data directory. Claude Code, Codex, the Web UI, REST API, and MCP should all connect to that process. The fallback stdio config in `.mcp.stdio.json` starts a separate `docgraph mcp --data .docgraph` process, so avoid using it at the same time as another DocGraph process that writes to the same data directory.
