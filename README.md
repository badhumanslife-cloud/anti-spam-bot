# Шлюхобот

Telegram group moderation bot running on Cloudflare Workers.

## Features

- Telegram webhook endpoint at `/telegram`
- Group enable/disable/status commands: `/setup`, `/status`, `/off`
- Admin-safe moderation: admins are skipped; anonymous admin commands are supported
- Local text rules for adult-service advertising and drug sales
- Groq Chat Completions integration using Qwen for additional moderation
- Deletes detected violations
- Replies to non-violating new group messages with a Tenor animation captioned **ШЛЮХОБОД БДИТ**
- Stores per-chat moderation state in Cloudflare KV

## Cloudflare setup

Create a Worker and add a KV namespace binding:

- Binding name: `BOT_DATA`

Add these secrets:

- `BOT_TOKEN` — Telegram bot token
- `WEBHOOK_SECRET` — Telegram webhook secret token
- `GROQ_API_KEY` — Groq API key

Deploy `worker.js`, then register the Telegram webhook pointing to:

`https://YOUR-WORKER.workers.dev/telegram`

Set Telegram's webhook secret token to the same value as `WEBHOOK_SECRET`.

Make the bot an administrator in the group and grant it permission to delete messages.

## Notes

- The bot examines text and media captions only; it does not visually inspect images or videos.
- Groq availability and rate limits depend on the current Groq account and model access. When Groq is unavailable, local rules continue to run.
- AI classification is heuristic and may make mistakes. Test in a private group before relying on it.
- Never commit API keys, bot tokens, webhook secrets, or Cloudflare credentials.
