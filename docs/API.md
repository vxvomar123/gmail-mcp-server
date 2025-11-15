# Gmail MCP Server - Complete API Reference

This document provides detailed information about all available tools in the Gmail MCP Server.

## Table of Contents

- [Email Operations](#email-operations)
  - [send_email](#send_email)
  - [draft_email](#draft_email)
  - [read_email](#read_email)
  - [search_emails](#search_emails)
  - [modify_email](#modify_email)
  - [delete_email](#delete_email)
- [Batch Operations](#batch-operations)
  - [batch_modify_emails](#batch_modify_emails)
  - [batch_delete_emails](#batch_delete_emails)
- [Label Management](#label-management)
  - [list_email_labels](#list_email_labels)
  - [create_label](#create_label)
  - [update_label](#update_label)
  - [delete_label](#delete_label)
- [Filter Management](#filter-management)
  - [create_filter](#create_filter)
  - [list_filters](#list_filters)
- [Attachment Operations](#attachment-operations)
  - [download_attachment](#download_attachment)

---

## Email Operations

### send_email

Send an email immediately with optional attachments, HTML content, and threading support.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `to` | string[] | Yes | Recipient email addresses |
| `subject` | string | Yes | Email subject line |
| `body` | string | Yes | Plain text email body |
| `htmlBody` | string | No | HTML version of the email body |
| `cc` | string[] | No | CC recipients |
| `bcc` | string[] | No | BCC recipients |
| `attachments` | string[] | No | Absolute file paths to attach (max 25MB per email) |
| `threadId` | string | No | Gmail thread ID to reply to |

**Example Usage:**

```typescript
// Basic email
{
  "to": ["colleague@example.com"],
  "subject": "Meeting Tomorrow",
  "body": "Hi! Just confirming our meeting at 2 PM tomorrow."
}

// Email with HTML and attachments
{
  "to": ["client@company.com"],
  "subject": "Q4 Report",
  "body": "Please find the Q4 report attached.",
  "htmlBody": "<p>Please find the <strong>Q4 report</strong> attached.</p>",
  "attachments": ["/Users/username/Documents/Q4-Report.pdf"]
}

// Reply to thread with CC
{
  "to": ["original-sender@example.com"],
  "subject": "Re: Project Update",
  "body": "Thanks for the update!",
  "cc": ["team@example.com"],
  "threadId": "18c5f8a3d9b2e1f0"
}
```

**Returns:**

```json
{
  "success": true,
  "messageId": "18c5f8a3d9b2e1f0",
  "threadId": "18c5f8a3d9b2e1f0"
}
```

**Errors:**

- Attachment file not found or inaccessible
- Attachment exceeds 25MB limit
- Invalid email addresses
- Authentication failure

---

### draft_email

Create a draft email without sending. Uses the same parameters as `send_email`.

**Parameters:** (Same as `send_email`)

**Example Usage:**

```typescript
{
  "to": ["team@company.com"],
  "subject": "Draft: Weekly Update",
  "body": "This week we accomplished...",
  "attachments": ["/Users/username/weekly-metrics.xlsx"]
}
```

**Returns:**

```json
{
  "success": true,
  "draftId": "r-12345678",
  "message": "Draft created successfully"
}
```

---

### read_email

Retrieve the full content of an email by its message ID, including headers, body, and attachment metadata.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `messageId` | string | Yes | Gmail message ID |

**Example Usage:**

```typescript
{
  "messageId": "18c5f8a3d9b2e1f0"
}
```

**Returns:**

```json
{
  "id": "18c5f8a3d9b2e1f0",
  "threadId": "18c5f8a3d9b2e1f0",
  "subject": "Project Update",
  "from": "colleague@example.com",
  "to": ["me@example.com"],
  "cc": [],
  "date": "2024-11-15T10:30:00Z",
  "snippet": "Here's the latest update on the project...",
  "body": "Full email body content here...",
  "htmlBody": "<html>...</html>",
  "labels": ["INBOX", "UNREAD"],
  "attachments": [
    {
      "attachmentId": "ANGjdJ8...",
      "filename": "report.pdf",
      "mimeType": "application/pdf",
      "size": 245678
    }
  ]
}
```

---

### search_emails

Search for emails using Gmail's powerful query syntax.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Gmail search query |
| `maxResults` | number | No | Maximum results to return (default: 10, max: 100) |

**Gmail Query Syntax:**

| Query | Description |
|-------|-------------|
| `from:user@example.com` | Emails from specific sender |
| `to:user@example.com` | Emails to specific recipient |
| `subject:meeting` | Emails with "meeting" in subject |
| `has:attachment` | Emails with attachments |
| `is:unread` | Unread emails |
| `is:starred` | Starred emails |
| `after:2024/01/01` | Emails after date |
| `before:2024/12/31` | Emails before date |
| `larger:10M` | Emails larger than 10MB |
| `smaller:1M` | Emails smaller than 1MB |
| `label:important` | Emails with specific label |
| `filename:pdf` | Emails with PDF attachments |

**Example Queries:**

```typescript
// Unread emails from boss
{
  "query": "from:boss@company.com is:unread",
  "maxResults": 20
}

// Large attachments from last week
{
  "query": "has:attachment larger:5M after:2024/11/08"
}

// Invoices from specific sender
{
  "query": "from:billing@vendor.com subject:invoice",
  "maxResults": 50
}
```

**Returns:**

```json
{
  "messages": [
    {
      "id": "18c5f8a3d9b2e1f0",
      "threadId": "18c5f8a3d9b2e1f0",
      "snippet": "Here's the latest invoice...",
      "internalDate": "1700050800000"
    }
  ],
  "resultSizeEstimate": 15
}
```

---

### modify_email

Change email labels to archive, mark as read/unread, or organize messages.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `messageId` | string | Yes | Gmail message ID |
| `addLabelIds` | string[] | No | Label IDs to add |
| `removeLabelIds` | string[] | No | Label IDs to remove |

**Common Label IDs:**

| Label ID | Description |
|----------|-------------|
| `INBOX` | Inbox |
| `UNREAD` | Unread |
| `STARRED` | Starred |
| `IMPORTANT` | Important |
| `TRASH` | Trash |
| `SPAM` | Spam |
| `SENT` | Sent |
| `DRAFT` | Draft |

**Example Usage:**

```typescript
// Mark as read and archive
{
  "messageId": "18c5f8a3d9b2e1f0",
  "removeLabelIds": ["UNREAD", "INBOX"]
}

// Star and mark as important
{
  "messageId": "18c5f8a3d9b2e1f0",
  "addLabelIds": ["STARRED", "IMPORTANT"]
}

// Move to custom label
{
  "messageId": "18c5f8a3d9b2e1f0",
  "addLabelIds": ["Label_123"],
  "removeLabelIds": ["INBOX"]
}
```

**Returns:**

```json
{
  "success": true,
  "messageId": "18c5f8a3d9b2e1f0",
  "labelIds": ["STARRED", "IMPORTANT"]
}
```

---

### delete_email

Permanently delete an email (move to trash).

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `messageId` | string | Yes | Gmail message ID |

**Example Usage:**

```typescript
{
  "messageId": "18c5f8a3d9b2e1f0"
}
```

**Returns:**

```json
{
  "success": true,
  "message": "Email deleted successfully"
}
```

---

## Batch Operations

### batch_modify_emails

Modify labels for multiple emails at once. Efficiently processes up to 50 emails per batch.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `messageIds` | string[] | Yes | Array of Gmail message IDs |
| `addLabelIds` | string[] | No | Label IDs to add |
| `removeLabelIds` | string[] | No | Label IDs to remove |
| `batchSize` | number | No | Batch size (default: 50, max: 50) |

**Example Usage:**

```typescript
// Archive multiple emails
{
  "messageIds": ["id1", "id2", "id3", "id4", "id5"],
  "removeLabelIds": ["INBOX"]
}

// Mark multiple as important and read
{
  "messageIds": ["id1", "id2", "id3"],
  "addLabelIds": ["IMPORTANT"],
  "removeLabelIds": ["UNREAD"]
}
```

**Returns:**

```json
{
  "success": true,
  "totalProcessed": 5,
  "successful": 5,
  "failed": 0,
  "results": [
    { "messageId": "id1", "success": true },
    { "messageId": "id2", "success": true },
    { "messageId": "id3", "success": true },
    { "messageId": "id4", "success": true },
    { "messageId": "id5", "success": true }
  ]
}
```

---

### batch_delete_emails

Delete multiple emails at once.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `messageIds` | string[] | Yes | Array of Gmail message IDs |
| `batchSize` | number | No | Batch size (default: 50, max: 50) |

**Example Usage:**

```typescript
{
  "messageIds": ["id1", "id2", "id3", "id4", "id5"],
  "batchSize": 25
}
```

**Returns:**

```json
{
  "success": true,
  "totalProcessed": 5,
  "successful": 5,
  "failed": 0
}
```

---

## Label Management

### list_email_labels

Get all available labels (both system and user-created).

**Parameters:** None

**Example Usage:**

```typescript
{}
```

**Returns:**

```json
{
  "labels": [
    {
      "id": "INBOX",
      "name": "INBOX",
      "type": "system"
    },
    {
      "id": "Label_123",
      "name": "Work Projects",
      "type": "user",
      "messageListVisibility": "show",
      "labelListVisibility": "labelShow"
    }
  ]
}
```

---

### create_label

Create a new Gmail label.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Label name |
| `messageListVisibility` | string | No | 'show' or 'hide' (default: 'show') |
| `labelListVisibility` | string | No | 'labelShow', 'labelShowIfUnread', or 'labelHide' (default: 'labelShow') |

**Example Usage:**

```typescript
// Simple label
{
  "name": "Important Clients"
}

// Label with custom visibility
{
  "name": "Newsletters",
  "messageListVisibility": "hide",
  "labelListVisibility": "labelShowIfUnread"
}
```

**Returns:**

```json
{
  "success": true,
  "labelId": "Label_456",
  "name": "Important Clients"
}
```

---

### update_label

Update an existing label's properties.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `labelId` | string | Yes | Label ID to update |
| `name` | string | No | New label name |
| `messageListVisibility` | string | No | 'show' or 'hide' |
| `labelListVisibility` | string | No | 'labelShow', 'labelShowIfUnread', or 'labelHide' |

**Example Usage:**

```typescript
{
  "labelId": "Label_456",
  "name": "VIP Clients",
  "messageListVisibility": "show"
}
```

---

### delete_label

Delete a user-created label.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `labelId` | string | Yes | Label ID to delete |

**Example Usage:**

```typescript
{
  "labelId": "Label_456"
}
```

---

## Filter Management

### create_filter

Create a Gmail filter to automate inbox organization.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `criteria` | object | Yes | Match conditions |
| `criteria.from` | string | No | From email address |
| `criteria.to` | string | No | To email address |
| `criteria.subject` | string | No | Subject contains |
| `criteria.query` | string | No | Gmail search query |
| `criteria.hasAttachment` | boolean | No | Has attachment |
| `criteria.size` | number | No | Size in bytes |
| `criteria.sizeComparison` | string | No | 'larger' or 'smaller' |
| `action` | object | Yes | Actions to perform |
| `action.addLabelIds` | string[] | No | Labels to add |
| `action.removeLabelIds` | string[] | No | Labels to remove |
| `action.forward` | string | No | Forward to email |

**Example Usage:**

```typescript
// Auto-label emails from boss
{
  "criteria": {
    "from": "boss@company.com"
  },
  "action": {
    "addLabelIds": ["Label_Important"]
  }
}

// Archive newsletters automatically
{
  "criteria": {
    "from": "newsletter@company.com"
  },
  "action": {
    "removeLabelIds": ["INBOX"],
    "addLabelIds": ["Label_Newsletters"]
  }
}

// Forward large attachments
{
  "criteria": {
    "hasAttachment": true,
    "size": 10485760,
    "sizeComparison": "larger"
  },
  "action": {
    "forward": "archive@company.com"
  }
}
```

**Returns:**

```json
{
  "success": true,
  "filterId": "ANGjdJ8wGCT...",
  "message": "Filter created successfully"
}
```

---

### list_filters

List all existing Gmail filters.

**Parameters:** None

**Returns:**

```json
{
  "filters": [
    {
      "id": "ANGjdJ8wGCT...",
      "criteria": {
        "from": "boss@company.com"
      },
      "action": {
        "addLabelIds": ["Label_Important"]
      }
    }
  ]
}
```

---

## Attachment Operations

### download_attachment

Download an email attachment to the local filesystem.

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `messageId` | string | Yes | Gmail message ID |
| `attachmentId` | string | Yes | Attachment ID (from read_email) |
| `savePath` | string | No | Download directory (default: `~/Downloads`) |
| `filename` | string | No | Custom filename (default: original filename) |

**Example Usage:**

```typescript
// Download with defaults
{
  "messageId": "18c5f8a3d9b2e1f0",
  "attachmentId": "ANGjdJ8..."
}

// Download to custom location
{
  "messageId": "18c5f8a3d9b2e1f0",
  "attachmentId": "ANGjdJ8...",
  "savePath": "/Users/username/Documents/Reports",
  "filename": "Q4-Report-2024.pdf"
}
```

**Returns:**

```json
{
  "success": true,
  "filename": "report.pdf",
  "path": "/Users/username/Downloads/report.pdf",
  "size": 245678
}
```

---

## Rate Limits

Gmail API has the following rate limits:

- **Quota**: 1 billion quota units per day
- **Per-user rate limit**: 250 quota units per second
- **Batch requests**: 100 requests per batch (we use 50 for safety)

**Quota Costs:**

| Operation | Quota Cost |
|-----------|-----------|
| Send email | 100 units |
| Read email | 5 units |
| Search | 5 units |
| Modify labels | 5 units |
| Create label | 5 units |
| Create filter | 5 units |

---

## Error Handling

All tools return structured error messages:

```json
{
  "success": false,
  "error": "Authentication failed",
  "details": "Token has expired. Please re-authenticate.",
  "solution": "Run: node dist/index.js auth --force"
}
```

Common error codes:
- `401`: Authentication required
- `403`: Permission denied (check test users)
- `404`: Message/label not found
- `429`: Rate limit exceeded
- `500`: Gmail API error

---

## Best Practices

### Performance

1. **Use batch operations** for multiple emails
2. **Limit search results** to what you need
3. **Cache label IDs** instead of looking them up repeatedly
4. **Use specific queries** to reduce search time

### Security

1. **Never commit credentials** to version control
2. **Validate file paths** before sending attachments
3. **Check attachment sizes** before sending
4. **Review OAuth scopes** periodically

### Reliability

1. **Handle token expiration** gracefully (automatic in v2.0)
2. **Implement retry logic** for transient failures
3. **Validate inputs** before making API calls
4. **Check quota usage** for high-volume operations

---

## Support

- **Documentation**: [README](../README.md)
- **Issues**: [GitHub Issues](https://github.com/devdattatalele/gmail-mcp-server/issues)
- **Email**: taleledevdatta@gmail.com
