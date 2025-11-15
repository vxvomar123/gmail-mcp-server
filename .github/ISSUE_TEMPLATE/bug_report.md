---
name: Bug Report
about: Report a bug or issue with Gmail MCP Server
title: '[BUG] '
labels: bug
assignees: ''
---

## Bug Description

A clear and concise description of what the bug is.

## Environment

**Operating System:**
- [ ] macOS
- [ ] Windows
- [ ] Linux

**Version Information:**
- OS Version:
- Node.js Version (`node --version`):
- Server Version (from `package.json`):

**Claude Desktop:**
- Claude Desktop Version:

## Steps to Reproduce

1. Go to '...'
2. Run command '....'
3. See error

## Expected Behavior

What you expected to happen.

## Actual Behavior

What actually happened.

## Error Messages

```
Paste any error messages here
```

## Configuration

**Claude Desktop Config** (remove sensitive paths):
```json
{
  "mcpServers": {
    "gmail": {
      "command": "node",
      "args": ["/path/to/gmail-mcp-server/dist/index.js"]
    }
  }
}
```

## Authentication Status

**Output of `node dist/index.js auth`** (redact any tokens):
```
Paste output here
```

## Additional Context

Add any other context about the problem here. Screenshots, logs, etc.

## Troubleshooting Steps Already Tried

- [ ] Re-authenticated with `node dist/index.js auth --force`
- [ ] Verified Google Cloud Console configuration
- [ ] Checked Claude Desktop config path and format
- [ ] Restarted Claude Desktop completely
- [ ] Reviewed [TROUBLESHOOTING.md](https://github.com/devdattatalele/gmail-mcp-server/blob/main/docs/TROUBLESHOOTING.md)
