# AI Daily Digest

Automated daily AI news digest powered by Claude Code.

## How it works

1. Scheduled Claude Code remote agent runs daily at 8:00 AM (Asia/Saigon)
2. Searches web for AI news, Claude Code updates, viral tools, etc.
3. Compiles digest and commits to `digests/YYYY-MM-DD.md`
4. GitHub Actions picks up the commit and sends to Telegram bot

## Setup

Add these secrets to the repo:
- `TELEGRAM_BOT_TOKEN` — bot token from @BotFather
- `TELEGRAM_CHAT_ID` — your Telegram user ID
