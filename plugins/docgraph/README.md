# DocGraph Plugin

This package contains the shared DocGraph plugin files for Claude Code and Codex.

The plugin connects agents to a running DocGraph HTTP MCP endpoint:

```sh
http://127.0.0.1:8787/mcp
```

Install DocGraph CLI separately before enabling the plugin. Start `docgraph serve` in the workspace that owns the `.docgraph` data directory. The first release does not download or bundle a DocGraph binary.

## Files

- `.mcp.json` configures Claude Code and Codex to connect to DocGraph over HTTP MCP.
- `.mcp.stdio.json` is an optional fallback for isolated stdio testing.
- `.codex-plugin/plugin.json` is the Codex plugin manifest.
- `.claude-plugin/plugin.json` is the Claude Code plugin manifest.
- `.mcp.json` stays at the plugin root for Claude Code plugin discovery.
- `assets/` contains the DocGraph icon and UI screenshots:
  - `icon.png`: DocGraph logo, 539x508.
  - `docgraph-dashboard.png`: dashboard/status view, 1440x1000.
  - `docgraph-connectors.png`: source management view, 1440x1000.
  - `docgraph-search.png`: search results view, 1440x861.

## Local Verification

From the workspace where you want DocGraph data:

```sh
docgraph --help
docgraph serve --data .docgraph --host 127.0.0.1 --port 8787
```

Then install or enable the plugin in Claude Code or Codex and confirm the host can see DocGraph MCP tools such as `doc_search`.
