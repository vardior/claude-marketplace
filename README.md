# orvardi-plugins

A collection of Claude Code plugins for enhanced development workflows.

## Available Plugins

| Plugin | Description |
|--------|-------------|
| [context7-agent](./context7-agent) | Context7 integration with a subagent-first workflow for fetching OSS library documentation |
| [ux-docs-generator](./ux-docs-generator) | Extract comprehensive UX documentation from code to power an AI product expert via RAG |

## Installation

### Add Marketplace

```bash
claude plugin marketplace add vardior/claude-marketplace
```

### Install a Plugin

```bash
claude plugin install context7-agent@orvardi-plugins
```

### Enable a Plugin

```bash
claude plugin enable context7-agent@orvardi-plugins
```

## For Development

Test a plugin locally:

```bash
claude --plugin-dir /path/to/claude-marketplace/context7-agent
```

Or validate the marketplace:

```bash
claude plugin validate /path/to/claude-marketplace
```

## Contributing

To add a new plugin:

1. Create a new directory with your plugin name
2. Add a `.claude-plugin/plugin.json` manifest
3. Add your plugin's agents, commands, hooks, or MCP servers
4. Update the root `marketplace.json` to include your plugin
5. Submit a pull request

## License

MIT
