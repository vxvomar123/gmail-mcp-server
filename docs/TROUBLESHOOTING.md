# Gmail MCP Server - Troubleshooting Guide

This guide covers common issues and their solutions. If you don't find your issue here, please [open an issue on GitHub](https://github.com/devdattatalele/gmail-mcp-server/issues).

## Table of Contents

- [Authentication Issues](#authentication-issues)
- [Server Startup Issues](#server-startup-issues)
- [Email Operation Issues](#email-operation-issues)
- [Claude Desktop Integration](#claude-desktop-integration)
- [Performance Issues](#performance-issues)
- [Advanced Debugging](#advanced-debugging)

---

## Authentication Issues

### Error: "403: Access blocked: This app's request is invalid"

**Cause:** Your app is in "Testing" mode and you haven't added yourself as a test user.

**Solution:**

1. Go to [Google Cloud Console - OAuth Consent Screen](https://console.cloud.google.com/apis/credentials/consent)
2. Scroll to the **"Test users"** section
3. Click **"ADD USERS"**
4. Enter your Gmail address (the one you want to use with this server)
5. Click **"Save"**
6. Run authentication again:
   ```bash
   node dist/index.js auth --force
   ```

**Why this happens:** Google restricts "Testing" apps to only allow specific users. You must explicitly add yourself.

---

### Error: "400: invalid_request" or "redirect_uri_mismatch"

**Cause:** The OAuth redirect URI in your credentials doesn't match what the server expects.

**Solution:**

1. Go to [Google Cloud Console - Credentials](https://console.cloud.google.com/apis/credentials)
2. Click on your OAuth 2.0 Client ID
3. Under **"Authorized redirect URIs"**, ensure you have:
   ```
   http://localhost:3000/oauth2callback
   ```
4. If it's missing or different, add/fix it
5. Click **"Save"**
6. Download the updated JSON credentials
7. Replace your `gcp-oauth.keys.json` file
8. Run authentication again:
   ```bash
   node dist/index.js auth --force
   ```

---

### Error: "Credentials are invalid or expired"

**Cause:** Your stored credentials are no longer valid (token expired, revoked, or corrupted).

**Solution:**

```bash
# Force re-authentication (clears old credentials)
node dist/index.js auth --force
```

This will:
- Remove existing credentials
- Open browser for fresh authentication
- Save new, valid credentials

---

### Error: "Token has been expired or revoked"

**Cause:** Credentials worked before but stopped working. Could be:
- Token naturally expired (should auto-refresh, but didn't)
- You revoked access in Google Account settings
- OAuth client was deleted/modified in Google Cloud

**Solutions:**

1. **Try auto-refresh first** (restart the server):
   ```bash
   # Restart Claude Desktop or re-run the server
   node dist/index.js
   ```

2. **If that doesn't work, re-authenticate**:
   ```bash
   node dist/index.js auth --force
   ```

3. **If still failing, check Google Cloud Console**:
   - Ensure OAuth client still exists
   - Verify scopes haven't changed
   - Check if API is still enabled

---

### Error: "Port 3000 already in use"

**Cause:** Another process is using port 3000 (needed for OAuth callback).

**Solution:**

**macOS/Linux:**
```bash
# Find and kill the process using port 3000
lsof -ti:3000 | xargs kill -9
```

**Windows:**
```bash
# Find the process
netstat -ano | findstr :3000

# Kill it (replace PID with actual process ID)
taskkill /PID <PID> /F
```

Then retry authentication.

---

### Error: "This app doesn't comply with Google's OAuth 2.0 policy"

**Cause:** OAuth consent screen is missing required information.

**Solution:**

1. Go to [OAuth Consent Screen](https://console.cloud.google.com/apis/credentials/consent)
2. Ensure these fields are filled:
   - **App name**: Gmail MCP Server
   - **User support email**: Your email
   - **Developer contact email**: Your email
   - **Privacy Policy URL**: (can use `https://policies.google.com/privacy` as placeholder)
   - **Terms of Service URL**: Optional
3. Click **"Save and Continue"**
4. Add scopes (if not already added):
   - `https://www.googleapis.com/auth/gmail.modify`
   - `https://www.googleapis.com/auth/gmail.settings.basic`
5. Add yourself as a test user
6. Retry authentication

---

### Browser doesn't open during authentication

**Cause:** Issue with the `open` package or system default browser.

**Solution:**

1. **Manual approach:**
   - Look for a URL in the terminal output (starts with `https://accounts.google.com/o/oauth2/v2/auth...`)
   - Copy and paste it into your browser manually
   - Complete authentication
   - The callback should work normally

2. **Check default browser:**
   ```bash
   # macOS
   open https://google.com  # Should open default browser

   # Linux
   xdg-open https://google.com

   # Windows
   start https://google.com
   ```

---

## Server Startup Issues

### Error: "Cannot find module 'googleapis'" or similar

**Cause:** Dependencies not installed or corrupted.

**Solution:**

```bash
# Clean install
rm -rf node_modules package-lock.json
npm install
npm run build
```

---

### Error: "gcp-oauth.keys.json not found"

**Cause:** OAuth credentials file is missing or in wrong location.

**Solution:**

1. **Check expected locations** (in order of priority):
   - `./gcp-oauth.keys.json` (project root)
   - `~/.gmail-mcp/gcp-oauth.keys.json` (home directory)

2. **Verify file name** (must be exact):
   - ✅ `gcp-oauth.keys.json`
   - ❌ `credentials.json`
   - ❌ `client_secret.json`
   - ❌ `gcp_oauth.keys.json`

3. **Download from Google Cloud Console**:
   - Go to [Credentials](https://console.cloud.google.com/apis/credentials)
   - Click your OAuth 2.0 Client ID
   - Click "Download JSON"
   - Rename to `gcp-oauth.keys.json`
   - Place in project root

---

### Error: "EACCES: permission denied" when accessing credentials

**Cause:** File permission issues on credential storage.

**Solution:**

```bash
# Fix permissions
chmod 600 ~/.gmail-mcp/credentials.json
chmod 700 ~/.gmail-mcp/
```

---

### Server starts but Claude Desktop doesn't see it

**Cause:** Configuration issue in Claude Desktop.

**Solution:**

1. **Verify config path**:
   - macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Windows: `%APPDATA%/Claude/claude_desktop_config.json`
   - Linux: `~/.config/Claude/claude_desktop_config.json`

2. **Check config format**:
   ```json
   {
     "mcpServers": {
       "gmail": {
         "command": "node",
         "args": ["/ABSOLUTE/PATH/TO/gmail-mcp-server/dist/index.js"]
       }
     }
   }
   ```

3. **Common mistakes**:
   - ❌ Relative path: `"./dist/index.js"`
   - ❌ Wrong slashes on Windows: `"C:\Users\..."` → use `"C:\\Users\\..."`
   - ✅ Absolute path: `"/Users/username/projects/gmail-mcp-server/dist/index.js"`

4. **Restart Claude Desktop completely**:
   - Quit application (not just close window)
   - Reopen

---

## Email Operation Issues

### Attachment won't send

**Possible Causes & Solutions:**

1. **File doesn't exist**:
   ```bash
   # Verify file exists
   ls -lh /path/to/attachment.pdf
   ```

2. **File too large** (Gmail limit: 25MB):
   ```bash
   # Check file size
   ls -lh /path/to/attachment.pdf

   # If over 25MB, compress or use Google Drive link instead
   ```

3. **Permission denied**:
   ```bash
   # Fix permissions
   chmod 644 /path/to/attachment.pdf
   ```

4. **Path issue**:
   - ✅ Absolute path: `/Users/username/Documents/file.pdf`
   - ❌ Relative path: `./file.pdf` or `~/Documents/file.pdf`
   - Use full, absolute paths

---

### Email not sending (no error)

**Debugging steps:**

1. **Check if it's in Drafts**:
   - You may have used `draft_email` instead of `send_email`

2. **Verify authentication**:
   ```bash
   # Test credentials
   node dist/index.js auth
   ```

3. **Check Gmail quota**:
   - Gmail limits: 500 emails/day for free accounts
   - Check [Google Account Activity](https://myaccount.google.com/activity)

4. **Look for validation errors**:
   - Invalid email addresses
   - Empty subject or body
   - Malformed HTML

---

### Search returns no results (but emails exist)

**Causes & Solutions:**

1. **Query syntax issue**:
   ```typescript
   // ❌ Wrong
   { "query": "from:user@example.com AND subject:test" }

   // ✅ Correct (no "AND")
   { "query": "from:user@example.com subject:test" }
   ```

2. **Date format**:
   ```typescript
   // ✅ Correct formats
   "after:2024/11/01"
   "after:2024-11-01"
   "after:11/01/2024"

   // ❌ Wrong
   "after:Nov 1, 2024"
   ```

3. **Case sensitivity**:
   - Gmail search is mostly case-insensitive
   - But use exact case for email addresses: `from:User@Example.com`

---

### Batch operation fails on some emails

**Expected behavior:** Batch operations continue even if individual emails fail.

**Check the response:**
```json
{
  "success": true,
  "totalProcessed": 10,
  "successful": 8,
  "failed": 2,
  "results": [
    { "messageId": "id1", "success": true },
    { "messageId": "id2", "success": false, "error": "Message not found" },
    // ...
  ]
}
```

**Common reasons for individual failures:**
- Message already deleted
- Invalid message ID
- Message in different account
- Rate limit hit mid-batch

---

### Label creation fails

**Possible Issues:**

1. **Label already exists**:
   ```bash
   # List existing labels first
   # Use list_email_labels tool
   ```

2. **Invalid name**:
   - ❌ Empty name
   - ❌ Name with leading/trailing spaces
   - ✅ Use regular characters, spaces in middle are OK

3. **Quota limit**:
   - Gmail limit: ~500 labels per account
   - Delete unused labels first

---

## Claude Desktop Integration

### Claude says "No tools available"

**Cause:** MCP server not properly connected.

**Solution:**

1. **Check Claude Desktop logs**:
   - macOS: `~/Library/Logs/Claude/`
   - Windows: `%APPDATA%/Claude/logs/`
   - Look for errors loading MCP server

2. **Verify server is running**:
   ```bash
   # Test server manually
   node dist/index.js
   # Should start without errors
   ```

3. **Check config syntax**:
   - Use [JSONLint](https://jsonlint.com/) to validate JSON
   - Ensure no trailing commas
   - Check quotes are straight (not curly)

---

### Claude sees server but commands fail

**Debugging:**

1. **Check authentication status**:
   ```bash
   node dist/index.js auth
   # Should say "Authentication successful" or guide you to re-auth
   ```

2. **Look at error messages**:
   - Claude should display specific error from server
   - Follow error guidance

3. **Restart both**:
   ```bash
   # 1. Quit Claude Desktop completely
   # 2. Reopen Claude Desktop
   # 3. Try command again
   ```

---

## Performance Issues

### Slow search results

**Optimizations:**

1. **Limit results**:
   ```typescript
   {
     "query": "from:user@example.com",
     "maxResults": 10  // Instead of 100
   }
   ```

2. **Use specific queries**:
   ```typescript
   // ❌ Slow (searches everything)
   "is:unread"

   // ✅ Faster (narrows down)
   "from:user@example.com is:unread after:2024/11/01"
   ```

3. **Check internet connection**:
   - Gmail API requires internet
   - Slow connection = slow operations

---

### Batch operations timeout

**Cause:** Processing too many emails at once.

**Solution:**

```typescript
// ❌ Too many at once
{
  "messageIds": [...1000 ids...],
  "batchSize": 50
}

// ✅ Process in smaller chunks
{
  "messageIds": [...100 ids...],
  "batchSize": 25
}
```

Or split into multiple batch operations.

---

### Server becomes unresponsive

**Debugging:**

1. **Check CPU/Memory**:
   ```bash
   # macOS/Linux
   top -p $(pgrep -f "gmail-mcp-server")

   # Windows
   tasklist | findstr node
   ```

2. **Restart server**:
   - Quit Claude Desktop
   - Wait 5 seconds
   - Reopen

3. **Clear credentials** (if persists):
   ```bash
   rm -rf ~/.gmail-mcp/credentials.json
   node dist/index.js auth --force
   ```

---

## Advanced Debugging

### Enable verbose logging

**Modify code temporarily** in `src/index.ts`:

```typescript
// Add at the top of file
const DEBUG = true;

// Add logging in problematic functions
if (DEBUG) {
  console.error('Debug info:', JSON.stringify(data, null, 2));
}
```

Then rebuild:
```bash
npm run build
```

---

### Check Gmail API quota

1. Go to [Google Cloud Console - APIs & Services - Dashboard](https://console.cloud.google.com/apis/dashboard)
2. Select your project
3. Click "Gmail API"
4. View quota usage
5. If at limit, wait 24 hours for reset

---

### Test authentication manually

```bash
# Navigate to project
cd gmail-mcp-server

# Run auth test
node dist/index.js auth

# Expected output:
# ✅ "Authentication successful!"
# ✅ "Credentials are valid and saved"

# If errors, follow the specific guidance shown
```

---

### Inspect stored credentials

```bash
# View credentials (for debugging only)
cat ~/.gmail-mcp/credentials.json

# Should contain:
# - access_token
# - refresh_token
# - expiry_date
# - token_type: "Bearer"
```

**Security Note:** Never share this file or commit it to git!

---

### Test individual operations

```typescript
// In Claude Desktop, try simple operations first:

// 1. List labels (simple, no parameters)
"List all my Gmail labels"

// 2. Search (simple query)
"Search for emails from gmail in the last week"

// 3. Send simple email
"Send a test email to myself with subject 'Test' and body 'Testing'"

// If these work, problem is with specific operation
// If these fail, problem is with authentication/setup
```

---

## Still Having Issues?

### Before opening an issue:

1. ✅ Tried re-authentication with `--force`
2. ✅ Checked Google Cloud Console configuration
3. ✅ Verified Claude Desktop config path and format
4. ✅ Restarted Claude Desktop completely
5. ✅ Checked this troubleshooting guide

### When opening an issue, include:

- **Environment**:
  - OS (macOS/Windows/Linux) and version
  - Node.js version: `node --version`
  - Server version: Check `package.json`

- **Error message**: Full text, including stack trace

- **Steps to reproduce**:
  1. What you did
  2. What you expected
  3. What actually happened

- **Config** (sanitized):
  ```json
  {
    "mcpServers": {
      "gmail": {
        "command": "node",
        "args": ["/ABSOLUTE/PATH/dist/index.js"]
      }
    }
  }
  ```

- **Authentication status**:
  ```bash
  node dist/index.js auth
  # Copy output (redact any tokens!)
  ```

### Get Help:

- 🐛 [Open an Issue](https://github.com/devdattatalele/gmail-mcp-server/issues/new)
- 💬 [GitHub Discussions](https://github.com/devdattatalele/gmail-mcp-server/discussions)
- 📧 Email: taleledevdatta@gmail.com

---

## Quick Reference

### Common Commands

```bash
# Re-authenticate
node dist/index.js auth --force

# Rebuild after changes
npm run build

# Check file locations
ls -la ~/.gmail-mcp/
ls -la ~/Library/Application\ Support/Claude/

# Test server manually
node dist/index.js

# View Claude Desktop logs (macOS)
tail -f ~/Library/Logs/Claude/mcp*.log
```

### File Locations

| File | Location |
|------|----------|
| OAuth Keys | `./gcp-oauth.keys.json` or `~/.gmail-mcp/gcp-oauth.keys.json` |
| Credentials | `~/.gmail-mcp/credentials.json` |
| Claude Config (macOS) | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Claude Config (Windows) | `%APPDATA%/Claude/claude_desktop_config.json` |
| Claude Config (Linux) | `~/.config/Claude/claude_desktop_config.json` |
| Claude Logs (macOS) | `~/Library/Logs/Claude/` |

---

**Most issues are authentication-related and solved by re-authenticating with `--force`!**
