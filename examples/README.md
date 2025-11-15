# Gmail MCP Server - Example Use Cases

This directory contains practical examples of how to use Gmail MCP Server with Claude Desktop.

## Quick Start Examples

All examples assume you've already:
1. Set up authentication (`node dist/index.js auth`)
2. Configured Claude Desktop with the server
3. Restarted Claude Desktop

Simply copy the prompts below and use them in Claude Desktop!

---

## Table of Contents

- [Basic Email Operations](#basic-email-operations)
- [Email Organization](#email-organization)
- [Automation & Filters](#automation--filters)
- [Advanced Use Cases](#advanced-use-cases)
- [Productivity Workflows](#productivity-workflows)

---

## Basic Email Operations

### Send a Simple Email

**Prompt:**
```
Send an email to john@example.com with subject "Meeting Tomorrow"
and body "Hi John, Just confirming our meeting at 2 PM tomorrow. Thanks!"
```

### Send Email with Attachment

**Prompt:**
```
Send an email to client@company.com with subject "Q4 Report"
and attach the file /Users/username/Documents/Q4-Report.pdf
Body: "Please find the Q4 report attached for your review."
```

### Draft Email for Later

**Prompt:**
```
Create a draft email to team@company.com with subject "Weekly Update"
Body: "This week we accomplished: [I'll fill this in later]"
```

### Reply to a Thread

**Prompt:**
```
Find the email from boss@company.com with subject "Project Update"
and reply with: "Thanks for the update! I'll have the report ready by Friday."
```

---

## Email Organization

### Archive Old Newsletters

**Prompt:**
```
Search for all emails from newsletter@medium.com from the last 3 months,
then archive them all.
```

### Label Important Emails

**Prompt:**
```
Find all unread emails from boss@company.com and label them as "Important"
```

### Bulk Delete Spam

**Prompt:**
```
Search for all emails with subject containing "Bitcoin" or "Crypto"
from the last month and delete them
```

### Mark Multiple as Read

**Prompt:**
```
Find all unread emails from notifications@github.com and mark them as read
```

---

## Automation & Filters

### Auto-Label Emails from Boss

**Prompt:**
```
Create a filter that automatically adds the "Important" label to
all emails from boss@company.com
```

### Auto-Archive Newsletters

**Prompt:**
```
Create a filter to automatically archive all emails from
newsletter@company.com and add the "Newsletters" label
```

### Filter Large Attachments

**Prompt:**
```
Create a filter for emails with attachments larger than 10MB
and forward them to archive@company.com
```

### Auto-Categorize by Subject

**Prompt:**
```
Create a filter that adds the "Invoices" label to any email
with "Invoice" or "Receipt" in the subject line
```

---

## Advanced Use Cases

### Download All Invoices

**Prompt:**
```
Search for all emails with subject containing "invoice" from the last year,
then download all PDF attachments to /Users/username/Documents/Invoices
```

### Generate Email Report

**Prompt:**
```
Search for all emails from client@company.com in the last month
and summarize: how many emails, main topics discussed, and any action items
```

### Organize by Project

**Prompt:**
```
Create labels for "Project-Alpha", "Project-Beta", and "Project-Gamma"
Then search for emails mentioning each project and apply the appropriate labels
```

### Clean Up Inbox

**Prompt:**
```
1. Archive all read emails older than 30 days
2. Delete all emails in trash
3. Create a filter to auto-archive promotional emails
4. Show me what's left in my inbox
```

---

## Productivity Workflows

### Morning Email Routine

**Prompt:**
```
Good morning! Help me organize my inbox:
1. Show me all unread important emails
2. Archive all newsletters
3. Label urgent emails from my team as "Today"
4. Summarize what needs my attention
```

### Weekly Cleanup

**Prompt:**
```
Weekly cleanup:
1. Archive all read emails from last week
2. Download all important attachments to my Archive folder
3. Delete old promotional emails
4. Show me a summary of action items for next week
```

### Client Email Management

**Prompt:**
```
For emails from client@company.com:
1. Create a "Client-CompanyName" label if it doesn't exist
2. Apply this label to all their emails
3. Search for any unanswered emails from them
4. Draft responses for any pending questions
```

### Travel Email Prep

**Prompt:**
```
I'm going on vacation. Help me prepare:
1. Search for any emails with "urgent" or "deadline" from my team
2. Create an out-of-office draft
3. Forward any time-sensitive emails to mycolleague@company.com
4. Archive everything else
```

---

## Specific Scenarios

### Research Email Thread

**Prompt:**
```
Find the email thread about "Budget 2024" and give me:
1. A summary of the discussion
2. Who's involved
3. Any decisions made
4. Action items assigned to me
```

### Attachment Management

**Prompt:**
```
Find all emails with Excel attachments from finance@company.com
in the last quarter, and download them to
/Users/username/Documents/Finance-Reports
```

### Email Migration

**Prompt:**
```
I'm switching from an old email. For all emails from oldclient@oldcompany.com:
1. Update my contact
2. Send them my new email
3. Label historical emails as "Legacy-OldCompany"
```

### Follow-up Reminder

**Prompt:**
```
Search for emails I sent in the last week that haven't received replies.
Create a list and draft follow-up emails for each.
```

---

## Advanced Automation

### Smart Newsletter Management

**Prompt:**
```
Create a comprehensive newsletter system:
1. Create a "Newsletters" label
2. Create filters for common newsletter domains
   (substack.com, medium.com, mailchimp.com)
3. Auto-archive and label these emails
4. Set them to skip inbox but keep unread for later reading
```

### Team Communication Organization

**Prompt:**
```
Set up labels and filters for my team:
1. Create labels: "Team-Dev", "Team-Design", "Team-Marketing"
2. Create filters to auto-label emails from each team
3. Star emails with "urgent" from any team member
4. Show me the current state of team communications
```

### Client Onboarding Automation

**Prompt:**
```
For new client newclient@company.com:
1. Create label "Client-NewCompany"
2. Send welcome email with onboarding info
3. Create filter to auto-label future emails from them
4. Set up filter to forward urgent emails from them to me via SMS
```

---

## Tips for Best Results

### 1. Be Specific with Paths

**Bad:**
```
Download the attachment to my Downloads folder
```

**Good:**
```
Download the attachment to /Users/username/Downloads
```

### 2. Use Date Ranges

**Bad:**
```
Find old emails
```

**Good:**
```
Find emails from January 2024 to March 2024
```

### 3. Combine Operations

**Bad:**
```
Find emails from boss
[Wait for response]
Now archive them
```

**Good:**
```
Find all read emails from boss@company.com and archive them
```

### 4. Specify Email Criteria Clearly

**Bad:**
```
Find important emails
```

**Good:**
```
Find unread emails with "urgent" in subject or body from my team
```

---

## Common Patterns

### Pattern: Search + Action

```
Find [criteria] and [action]

Examples:
- "Find all starred emails from last month and export to PDF"
- "Find emails with attachments from client@company.com and download them"
- "Find all emails with label 'Old-Project' and delete them"
```

### Pattern: Create + Apply

```
Create [resource] and apply to [target]

Examples:
- "Create a 'Urgent' label and apply it to unread emails from my boss"
- "Create a filter for newsletters and apply it to existing emails"
- "Create a 'Clients' label and organize all client emails under it"
```

### Pattern: Batch Processing

```
For all [criteria], [action]

Examples:
- "For all unread promotional emails, mark as read and archive"
- "For all emails with PDF attachments from last year, download to Archive"
- "For all emails in 'Old-Project' label, remove label and archive"
```

---

## Integration with Other Tools

### Export to Note-Taking App

**Prompt:**
```
Find all emails tagged "Ideas" and create a summary I can paste into my notes:
- Date
- From
- Subject
- Key points
```

### Calendar Integration

**Prompt:**
```
Search for emails with words like "meeting", "call", or "schedule"
from the last week that I haven't responded to yet.
List them with suggested times to reply.
```

### Task Management

**Prompt:**
```
Find emails marked "TODO" and create a task list:
- Priority level (based on sender and subject)
- Due date (if mentioned)
- Brief description
- Email link
```

---

## Troubleshooting Common Prompts

### If Claude says "I can't find the file"

**Try:**
```
Use the absolute path: /Users/username/Documents/file.pdf
Not relative paths like: ~/Documents/file.pdf or ./file.pdf
```

### If search returns no results

**Try:**
```
Be more specific or use different criteria:
- Instead of "find important emails" → "find emails with label:IMPORTANT"
- Instead of "recent emails" → "emails from the last 7 days"
- Instead of "from John" → "from john@company.com"
```

### If batch operations fail

**Try:**
```
Process smaller batches:
- Instead of "archive all emails from last year"
- Try "archive all emails from January 2024"
- Then repeat for other months
```

---

## Need More Help?

- **API Reference**: See [docs/API.md](../docs/API.md) for all available operations
- **Troubleshooting**: Check [docs/TROUBLESHOOTING.md](../docs/TROUBLESHOOTING.md)
- **Issues**: [GitHub Issues](https://github.com/devdattatalele/gmail-mcp-server/issues)

---

## Contributing Examples

Have a great use case? Share it!

1. Fork the repository
2. Add your example to this file
3. Submit a pull request

**Template:**
```markdown
### Your Use Case Title

**Prompt:**
\`\`\`
Your example prompt here
\`\`\`

**What it does:**
Brief explanation

**Why it's useful:**
The problem it solves
\`\`\`
```

---

**Happy automating!** 🚀
