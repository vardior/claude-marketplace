# code-review

AI-powered local code review for branch changes with bug detection and CLAUDE.md compliance checking.

This plugin is a local-run adaptation of the official [code-review plugin](https://github.com/anthropics/claude-code/tree/main/plugins/code-review) from the Claude Code repository.

## Features

- Reviews branch changes against a base branch (defaults to `main`)
- Checks for CLAUDE.md compliance in modified files
- Detects bugs, security issues, and incorrect logic
- Uses parallel agents for thorough review
- Validates issues to minimize false positives

## Usage

```bash
/code-review [base-branch]
```

If no base branch is specified, defaults to `main` (or `master` if `main` doesn't exist).

## How It Works

1. Compares current branch to the base branch
2. Finds relevant CLAUDE.md files for context
3. Launches parallel agents to review for:
   - CLAUDE.md compliance violations
   - Bugs and logic errors
   - Security issues
4. Validates all flagged issues to ensure high signal
5. Outputs a formatted review report
