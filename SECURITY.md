# Security Policy

## Supported Versions

We actively maintain and provide security updates for the following versions:

| Version | Supported          |
| ------- | ------------------ |
| 2.0.x   | :white_check_mark: |
| < 2.0   | :x:                |

## Security Considerations

### OAuth Credentials

**CRITICAL:** Never commit OAuth credentials or tokens to version control.

**Protected Files:**
- `gcp-oauth.keys.json` - Your OAuth client credentials
- `~/.gmail-mcp/credentials.json` - Your access/refresh tokens

Both files are automatically ignored by `.gitignore`. If you modify `.gitignore`, ensure these remain excluded.

**What to do if credentials are exposed:**

1. **Immediately revoke access:**
   - Visit [Google Account Permissions](https://myaccount.google.com/permissions)
   - Remove "Gmail MCP Server" from authorized apps

2. **Delete OAuth client:**
   - Go to [Google Cloud Console - Credentials](https://console.cloud.google.com/apis/credentials)
   - Delete the exposed OAuth 2.0 Client ID
   - Create a new one

3. **Rotate all credentials:**
   ```bash
   rm ~/.gmail-mcp/credentials.json
   rm gcp-oauth.keys.json
   # Create new OAuth client and re-authenticate
   node dist/index.js auth --force
   ```

4. **If committed to git:**
   - Rewrite git history to remove the credentials
   - Force push (if remote repository)
   - Consider the credentials permanently compromised

---

## Reporting a Vulnerability

### Where to Report

**DO NOT** create a public GitHub issue for security vulnerabilities.

Instead, report vulnerabilities privately via:

- **Email**: taleledevdatta@gmail.com
- **Subject**: `[SECURITY] Gmail MCP Server - [Brief Description]`

### What to Include

Please provide:

1. **Description** of the vulnerability
2. **Steps to reproduce** (proof of concept)
3. **Potential impact** (what could an attacker do?)
4. **Suggested fix** (if you have one)
5. **Your contact information** (for follow-up)

**Example Report:**

```
Subject: [SECURITY] Gmail MCP Server - Token Exposure in Logs

Description:
Access tokens are being logged to console.error in production
builds when authentication fails.

Steps to Reproduce:
1. Cause authentication failure
2. Check console output
3. Observe access token in error message

Potential Impact:
Tokens could be exposed in server logs, CI/CD outputs, or
monitoring systems, allowing unauthorized access to user Gmail.

Suggested Fix:
Redact tokens before logging: token.replace(/./g, '*')

Contact: security-researcher@example.com
```

### Response Timeline

- **Acknowledgment**: Within 48 hours
- **Initial Assessment**: Within 5 business days
- **Status Updates**: Weekly, or as developments occur
- **Fix Timeline**: Depends on severity (see below)

### Severity Levels

| Severity | Response Time | Examples |
|----------|--------------|----------|
| **Critical** | 24-48 hours | Token exposure, remote code execution |
| **High** | 1 week | Authentication bypass, data leakage |
| **Medium** | 2-4 weeks | Denial of service, information disclosure |
| **Low** | Best effort | Minor information leaks, edge cases |

---

## Security Best Practices

### For Users

#### 1. Credential Storage

✅ **DO:**
- Store credentials in `~/.gmail-mcp/` with user-only permissions
- Use absolute paths for OAuth keys
- Regularly review authorized apps in Google Account

❌ **DON'T:**
- Store credentials in project directory (unless gitignored)
- Share credentials or OAuth keys
- Commit credentials to version control

#### 2. OAuth Scopes

We request minimal necessary scopes:
- `https://www.googleapis.com/auth/gmail.modify` - Email operations
- `https://www.googleapis.com/auth/gmail.settings.basic` - Label/filter management

**Review regularly:**
1. Visit [Google Account Permissions](https://myaccount.google.com/permissions)
2. Check "Gmail MCP Server" permissions
3. Ensure only expected scopes are authorized

#### 3. File Attachments

When sending attachments:

✅ **DO:**
- Validate file paths before sending
- Check file sizes (< 25MB)
- Verify file contents are intended

❌ **DON'T:**
- Use user input directly as file paths
- Send files from sensitive directories
- Blindly trust attachment metadata

#### 4. Email Content

When processing emails:

✅ **DO:**
- Sanitize HTML email content if displaying
- Validate email addresses
- Be cautious with email forwarding rules

❌ **DON'T:**
- Execute code from email content
- Trust "From" addresses without verification
- Auto-download all attachments

### For Developers

#### 1. Input Validation

```typescript
// ✅ Good
function validateEmail(email: string): boolean {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return emailRegex.test(email);
}

// ❌ Bad
function sendEmail(to: any) {
  gmail.send({ to }); // No validation!
}
```

#### 2. Error Handling

```typescript
// ✅ Good - Don't expose sensitive data
catch (error) {
  console.error('Authentication failed');
  // Log to secure location, not console
}

// ❌ Bad - Exposes tokens
catch (error) {
  console.error('Auth failed:', error.response); // May contain tokens!
}
```

#### 3. Token Management

```typescript
// ✅ Good - Automatic refresh
if (credentials.expiry_date <= Date.now()) {
  const { credentials: newCreds } = await oauth2Client.refreshAccessToken();
  saveCredentials(newCreds);
}

// ❌ Bad - No refresh, stale tokens
oauth2Client.setCredentials(credentials); // May be expired!
```

#### 4. File Operations

```typescript
// ✅ Good - Validate paths
import path from 'path';

function validateFilePath(filePath: string): boolean {
  const resolved = path.resolve(filePath);
  // Ensure not accessing sensitive system files
  const forbidden = ['/etc', '/var', '/System'];
  return !forbidden.some(dir => resolved.startsWith(dir));
}

// ❌ Bad - No validation
function attachFile(filePath: string) {
  const content = fs.readFileSync(filePath); // Arbitrary file access!
}
```

---

## Known Security Considerations

### 1. Token Storage

**Issue:** Tokens stored in plaintext in `~/.gmail-mcp/credentials.json`

**Mitigation:**
- File permissions restricted to user only (chmod 600)
- Location outside project directory
- Not committed to version control

**Future:** Consider encrypted token storage

### 2. OAuth Flow

**Issue:** OAuth callback runs local web server on port 3000

**Mitigation:**
- Server only runs during authentication
- Listens only on localhost
- Shuts down immediately after callback

**Note:** Ensure port 3000 is not exposed to network

### 3. Email Content

**Issue:** Email content (including HTML) is processed without sanitization

**Mitigation:**
- Content only passed to Gmail API, not rendered
- No code execution from email content
- Attachments validated before download

**User Responsibility:** Be cautious when downloading unknown attachments

### 4. Rate Limiting

**Issue:** No client-side rate limiting for Gmail API

**Mitigation:**
- Google enforces server-side rate limits
- Batch operations use safe batch sizes (50 max)
- Errors returned for quota exceeded

**User Responsibility:** Monitor quota usage in Google Cloud Console

---

## Security Updates

Security fixes are released as:

1. **Patch versions** (2.0.x) for minor issues
2. **Minor versions** (2.x.0) for moderate issues
3. **Major versions** (x.0.0) for critical issues requiring breaking changes

**Stay Updated:**
- Watch this repository for releases
- Enable GitHub notifications for security advisories
- Check [CHANGELOG.md](CHANGELOG.md) regularly

---

## Security Checklist for Production Use

Before deploying in production:

- [ ] OAuth credentials stored securely
- [ ] `.gitignore` includes credential files
- [ ] File permissions set correctly (chmod 600 for credentials)
- [ ] OAuth scopes reviewed and minimal
- [ ] Input validation implemented for user-provided data
- [ ] Error messages don't expose sensitive information
- [ ] Logs reviewed for credential leakage
- [ ] Dependencies regularly updated (`npm audit`)
- [ ] Google Account permissions reviewed monthly
- [ ] Backup authentication method configured

---

## Compliance

### GDPR

This software:
- Processes email data locally (not stored by server)
- Uses direct Gmail API (no third-party data sharing)
- Respects user data minimization (minimal OAuth scopes)
- Allows user to revoke access anytime

**Your Responsibility:**
- Inform users about email data processing
- Obtain consent for automated email operations
- Provide data deletion mechanism
- Document data retention policies

### Other Regulations

If subject to specific regulations (HIPAA, PCI-DSS, etc.):
- **DO NOT** use this server for regulated data without proper security review
- Implement additional encryption for sensitive emails
- Consider dedicated Gmail accounts for regulated data
- Consult legal/compliance team before deployment

---

## Questions?

For security questions (non-vulnerabilities):
- 💬 [GitHub Discussions](https://github.com/devdattatalele/gmail-mcp-server/discussions)
- 📧 Email: taleledevdatta@gmail.com

For vulnerabilities, always use private reporting methods described above.

---

**Security is a shared responsibility.** Thank you for helping keep Gmail MCP Server safe!
