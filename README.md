# Slop Detector

A Claude Code plugin that detects AI-generated emails and labels them as "slop" in Gmail.

Uses [Pangram Labs](https://pangram.com) for AI text detection.

## Installation

### 1. Prerequisites

- [Claude Code](https://claude.com/claude-code) installed
- Gmail account
- [Pangram Labs API key](https://pangram.com) ($5 for 100 credits to start)

### 2. Set up Gmail MCP

Add the Gmail MCP server to Claude Code:

```bash
claude mcp add gmail -- npx -y @gongrzhe/server-gmail-autoauth-mcp
```

Restart Claude Code, then authenticate with your Gmail account when prompted.

### 3. Set Pangram API Key

```bash
export PANGRAM_API_KEY=your-api-key-here
```

Add to your shell profile (`~/.zshrc` or `~/.bashrc`) for persistence:

```bash
echo 'export PANGRAM_API_KEY=your-api-key-here' >> ~/.zshrc
```

### 4. Install the Plugin

```bash
/plugin marketplace add sudhanshuheda/slop-detector
/plugin install slop-detector
```

## Usage

Scan your recent emails for AI slop:

```
/slop-detector:scan
```

This will:
1. Fetch unread emails from the last 24 hours
2. Analyze each email with Pangram Labs AI detection
3. Apply a "slop" label to emails detected as AI-generated (>70% AI score)
4. Report a summary of findings

## How It Works

The plugin sends email body text to Pangram Labs' AI detection API. Emails scoring above 70% on the `fraction_ai` metric are labeled as slop.

**Detection threshold**: 0.7 (70% AI likelihood)

## Cost

- Gmail MCP: Free (uses your Google account)
- Pangram Labs: ~1 credit per email scanned
  - $5 = 100 credits
  - $100/month = 2000 credits (Developer plan)

## Troubleshooting

### "PANGRAM_API_KEY not set"
Set the environment variable before running Claude Code:
```bash
export PANGRAM_API_KEY=your-key
claude
```

### Gmail MCP not connecting
1. Restart Claude Code after adding the MCP
2. Check MCP status: `claude mcp list`
3. Re-authenticate if needed

### False positives
The 0.7 threshold can be adjusted in the skill. Lower = more aggressive detection, higher = fewer false positives.

## License

MIT
