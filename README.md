# Шлюхобот

Telegram group moderation bot running on Cloudflare Workers.

## Features

- Telegram webhook endpoint at /telegram
- Group enable/disable/status commands: /setup, /status, /off
- Admin-safe moderation: admins are skipped; anonymous admin commands are supported
- Local text rules for adult-service advertising and drug sales
- Groq Chat Completions integration using Qwen 3.8 27B for text and visual moderation
- GIF, Telegram animation, video, round video note, and MP4/GIF document inspection
- Up to three JPEG frames sampled from the start, middle, and end of media
- Cloudflare Media Transformations binding extracts frames
- Deletes detected violations
- Replies to non-violating new group messages with a Tenor animation captioned **ШЛЮХОБОД БДИТ**
- Stores per-chat moderation state in Cloudflare KV

## Cloudflare setup

Create a Worker and add a KV namespace binding:

- Binding name: BOT_DATA

Add a Media Transformations binding:

- Binding name: MEDIA

Wrangler configuration:

    [media]
    binding = "MEDIA"

Add these secrets:

- BOT_TOKEN — Telegram bot token
- WEBHOOK_SECRET — Telegram webhook secret token
- GROQ_API_KEY — Groq API key

Deploy worker.js, then register the Telegram webhook pointing to:

https://YOUR-WORKER.workers.dev/telegram

Set Telegram's webhook secret token to the same value as WEBHOOK_SECRET.

Make the bot an administrator in the group and grant it permission to delete messages.

## Media limits and behavior

- The bot downloads media through the standard Telegram Bot API, which currently limits downloads to 20 MB.
- Cloudflare Media Transformations supports videos up to 10 minutes and 100 MB; the Telegram download limit is stricter here.
- Up to three frames are sampled at the start, middle, and end. Intermediate or very brief content may be missed.
- The Worker must have the Cloudflare Media binding named exactly MEDIA.
- If media extraction fails or the binding is absent, text moderation still runs; media-only posts are not visually classified.
- Media captions are checked alongside extracted frames.
- Groq model access and rate limits depend on the current Groq account and model availability.

## Security

Never commit API keys, bot tokens, webhook secrets, or Cloudflare credentials.
