# Context7 Plugin for Claude Code

A Claude Code plugin that provides Context7 integration with a subagent-first workflow for fetching OSS library documentation.

## Features

- **Context7 MCP Server**: Automatically configures the Context7 MCP server for library documentation lookup
- **context7-researcher Subagent**: Specialized agent for thorough documentation research using Haiku model
- **Smart Workflow Hook**: After `resolve-library-id` is called, nudges the main agent to delegate the expensive `get-library-docs` call to the subagent

## Installation

### For Testing (Development)

```bash
claude --plugin-dir /path/to/context7-plugin
```

### For Permanent Installation

```bash
claude plugin install /path/to/context7-plugin --scope user
```

## How It Works

1. **resolve-library-id** (cheap call) - Can be called from main conversation
2. **PostToolUse hook triggers** - Reminds Claude to use the subagent
3. **get-library-docs** (expensive call) - Delegated to `context7:context7-researcher` subagent running on Haiku

This workflow optimizes costs by running the expensive documentation fetching on the cheaper Haiku model while keeping the main conversation on your preferred model.

## Usage

When you need library documentation, Claude will:

1. Call `resolve-library-id` to find the library
2. Receive a reminder to use the subagent
3. Spawn `context7:context7-researcher` to fetch detailed docs
4. Return synthesized documentation to you

You can also explicitly request: "Use the context7-researcher agent to look up [library] documentation"

## License

MIT
