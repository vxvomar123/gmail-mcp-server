# Changelog

All notable changes to the Gmail MCP Server will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.0.0] - 2024-11-15

### 🎉 Major Release - Complete Rewrite

This is a complete overhaul of the Gmail MCP Server with focus on reliability, authentication, and user experience.

### Added

#### Authentication & Security
- **Intelligent token management** - Automatic validation on server startup
- **Auto-refresh mechanism** - Expired tokens are refreshed automatically
- **Force re-authentication** - `--force` flag to start fresh
- **Pre-flight checklist** - Guided authentication setup
- **Comprehensive error messages** - Actionable solutions for common auth issues
- **403 error handling** - Step-by-step guide for "Access blocked" errors

#### Email Operations
- **Attachment support** - Send and receive files up to 25MB
- **Download attachments** - Save attachments to local filesystem
- **HTML email support** - Full multipart MIME handling
- **Email threading** - Reply to existing conversation threads
- **Draft management** - Create drafts without sending
- **Advanced search** - Full Gmail query syntax support
- **Batch operations** - Process up to 50 emails at once
  - `batch_modify_emails` - Label multiple emails efficiently
  - `batch_delete_emails` - Delete multiple emails at once

#### Organization & Automation
- **Label management** - Complete CRUD operations
  - Create labels with visibility settings
  - Update existing labels
  - Delete labels
  - List all labels (system + user-created)
- **Filter management** - Automate inbox organization
  - Create filters with custom criteria
  - Pre-built filter templates
  - List and manage existing filters
- **Modify operations** - Archive, mark as read/unread, add/remove labels

#### Developer Experience
- **TypeScript rewrite** - Full type safety throughout codebase
- **Comprehensive error handling** - Graceful failure with helpful messages
- **Detailed logging** - Better debugging and troubleshooting
- **Code documentation** - Inline comments and clear structure
- **Development mode** - Watch mode for faster iteration

#### Documentation
- **Complete README** - Step-by-step setup guide
- **Troubleshooting section** - Solutions for common problems
- **API reference** - Detailed tool documentation with examples
- **Comparison guide** - Why choose this over alternatives
- **Contributing guide** - How to contribute to the project

### Changed

- **Simplified dependencies** - Removed unnecessary packages (no OpenAI requirement)
- **Improved credential storage** - Better path handling and permissions
- **Enhanced server initialization** - Token validation on startup
- **Better error messages** - From generic to specific with solutions
- **OAuth flow improvements** - More reliable redirect handling

### Fixed

- **Broken authentication** - Complete fix for OAuth flow issues
- **Token expiration crashes** - Auto-refresh prevents server failures
- **Missing test user errors** - Clear guidance on fixing 403 errors
- **Port conflicts** - Better handling of OAuth callback port
- **Credential path issues** - Consistent path resolution
- **Attachment encoding** - Proper base64 handling for files

### Security

- **Credential encryption** - User-only file permissions for stored tokens
- **Scope minimization** - Only request necessary Gmail permissions
- **No third-party AI** - Direct Gmail API (no data routing through external services)
- **Gitignore credentials** - Prevents accidental credential commits

## [1.0.0] - Previous Version

### Initial Implementation (from original repository)

- Basic email sending capability
- Simple OAuth authentication
- Read email functionality
- Basic search support

**Note**: This version had significant authentication issues and limited features. Version 2.0.0 represents a complete rewrite addressing these problems.

---

## Unreleased

### Planned Features

- [ ] Multi-account support
- [ ] Email templates system
- [ ] Signature management
- [ ] Calendar integration (meeting invites)
- [ ] Improved threading (full conversation view)
- [ ] Spam filter management
- [ ] Email scheduling
- [ ] Read receipts tracking
- [ ] Automated testing suite
- [ ] Performance optimizations for large batches

### Under Consideration

- [ ] Caching layer for frequently accessed emails
- [ ] Webhook support for real-time updates
- [ ] Email preview/summary generation
- [ ] Smart categorization
- [ ] Undo send functionality
- [ ] Email analytics and insights

---

## Release Notes

### How to Upgrade

#### From 1.x to 2.0.0

**Breaking Changes:**
- Credential storage location changed to `~/.gmail-mcp/`
- OAuth keys now expected as `gcp-oauth.keys.json`
- Server command line interface updated

**Migration Steps:**

1. **Backup your existing credentials:**
   ```bash
   cp ~/.gmail-mcp-credentials.json ~/backup-credentials.json
   ```

2. **Install version 2.0.0:**
   ```bash
   cd gmail-mcp-server
   git pull origin main
   npm install
   npm run build
   ```

3. **Re-authenticate** (recommended for clean start):
   ```bash
   node dist/index.js auth --force
   ```

4. **Update Claude Desktop config** (if needed):
   ```json
   {
     "mcpServers": {
       "gmail": {
         "command": "node",
         "args": ["/absolute/path/to/gmail-mcp-server/dist/index.js"]
       }
     }
   }
   ```

5. **Restart Claude Desktop**

**New Features You Can Use:**
- Try batch operations: "Archive all unread emails from last week"
- Download attachments: "Download all PDFs from emails in the last month"
- Create filters: "Auto-label all emails from my boss as Important"
- Use HTML in emails: "Send a styled email with a table showing Q4 results"

---

## Version History

| Version | Release Date | Highlights |
|---------|-------------|------------|
| 2.0.0 | 2024-11-15 | Complete rewrite, bulletproof auth, batch operations |
| 1.0.0 | 2024-XX-XX | Initial release (original repository) |

---

## Contributing

Found a bug or want to suggest a feature?
- **Report bugs**: [GitHub Issues](https://github.com/devdattatalele/gmail-mcp-server/issues)
- **Suggest features**: [GitHub Discussions](https://github.com/devdattatalele/gmail-mcp-server/discussions)
- **Submit PRs**: See [CONTRIBUTING.md](CONTRIBUTING.md)

---

**[View all releases on GitHub](https://github.com/devdattatalele/gmail-mcp-server/releases)**
