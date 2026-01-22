---
name: scan
description: Scans recent emails for AI-generated content and labels detected slop
---

# AI Slop Detector

Scan recent emails for AI-generated content using Pangram Labs API and apply the "slop" label to detected emails.

## Prerequisites Check

Before scanning, verify:
1. `PANGRAM_API_KEY` environment variable is set
2. Gmail MCP server is connected and authenticated

If `PANGRAM_API_KEY` is not set, inform the user:
```
Set your Pangram Labs API key: export PANGRAM_API_KEY=your-api-key
Get an API key at https://pangram.com
```

## Workflow

### Step 1: Ensure "slop" label exists
Use Gmail MCP to list labels. If "slop" label doesn't exist, create it.

### Step 2: Fetch recent unread emails
Use Gmail MCP to search for emails:
- Query: `is:unread newer_than:1d`
- Limit to 20 emails per scan to avoid rate limits

### Step 3: Analyze each email
For each email:

1. Get the email body text (prefer plain text, fall back to stripped HTML)

2. Call Pangram Labs API:
```bash
curl -s -X POST 'https://text.api.pangram.com/v3' \
  -H 'Content-Type: application/json' \
  -H "x-api-key: $PANGRAM_API_KEY" \
  -d '{"text": "<email_body_here>"}'
```

3. Parse response - check `fraction_ai` field:
   - If `fraction_ai > 0.7`: This is likely AI-generated slop
   - If `fraction_ai <= 0.7`: Probably human-written

### Step 4: Label detected slop
For emails where `fraction_ai > 0.7`:
- Apply the "slop" label using Gmail MCP
- Track: sender, subject, AI score

### Step 5: Report results
Summarize findings:
```
Scanned: X emails
AI Slop Detected: Y emails
  - [sender]: [subject] (score: 0.XX)
  - [sender]: [subject] (score: 0.XX)
Human Written: Z emails
```

## Notes

- Emails shorter than 50 characters are skipped (not enough text to analyze)
- The 0.7 threshold balances detection accuracy with false positives
- Each Pangram API call uses 1 credit for short texts
