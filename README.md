# Nimply plugin for Grok Build

Manage your social media from Grok Build. This plugin connects Grok to [Nimply](https://nimply.io), a social media management platform, through Nimply's hosted MCP server, and ships a skill that teaches Grok how to use it well.

What you can do once connected:

- List connected social accounts (Facebook, Instagram, Threads, X, LinkedIn, TikTok, YouTube, Pinterest, Google Business Profile, Telegram) and see which need reconnecting.
- Create posts as drafts, queue them into each channel's next free posting slot, schedule them for a specific time, or publish now.
- Use platform-specific options for YouTube, TikTok, Pinterest and LinkedIn.
- Browse, edit, reschedule, unschedule or delete drafts and scheduled posts.
- Run the approval workflow: request approval, approve or reject with comments.
- Read workspace, channel and post analytics.
- Manage Nimply Pages link-in-bio pages (nimply.link): list pages, add or change blocks, publish.

## Install

From the Grok Build marketplace (once listed):

```
grok plugin install nimply --trust
```

Or load this folder directly:

```
grok --plugin-dir /path/to/grok-plugin
```

## Authentication

On first use Grok opens Nimply's authorization page in your browser. Sign in, choose the workspace Grok may use, review the permissions, and click **Allow access**. The token is scoped to that one workspace and can be revoked at any time from **Settings → Connected apps** in Nimply. You can also authenticate from the `/mcps` modal in Grok Build.

For non-interactive setups you may instead send a Nimply API key (created in Nimply under **Settings → Developers**) as `Authorization: Bearer nim_live_...`; see the [developer docs](https://developer.nimply.io/docs/integrations/grok).

## Network endpoints

This plugin contains no executable code. It declares one remote MCP server and one skill. At runtime Grok talks to:

- `https://mcp.nimply.io/mcp` — the Nimply MCP server (tool calls)
- `https://api.nimply.io` — Nimply's public API and OAuth token endpoint, called by the MCP server and during authentication
- `https://app.nimply.io/oauth/authorize` — the consent page opened in your browser during authentication

No other hosts are contacted. Nimply receives only the tool calls Grok makes, never your conversation. Privacy policy: https://nimply.io/privacy#developer-api

## Tools

28 tools: `list_channels`, `get_posting_schedule`, `create_post`, `create_youtube_video`, `create_tiktok_post`, `create_pinterest_pin`, `create_linkedin_post`, `get_pinterest_boards`, `get_tiktok_creator_info`, `list_posts`, `get_post`, `update_post`, `schedule_post`, `unschedule_post`, `publish_post`, `delete_post`, `request_approval`, `approve_post`, `reject_post`, `upload_media`, `create_media_upload`, `complete_media_upload`, `get_analytics`, `list_link_pages`, `get_link_page`, `add_link_block`, `update_link_block`, `publish_link_page`. Every tool carries MCP annotations (read-only / destructive / open-world) so Grok can ask before anything goes live. Full reference: https://developer.nimply.io/docs/mcp

## Requirements

A Nimply account (the free plan works) with at least one connected social channel. The person connecting must be an owner or admin of the workspace.

## Support

hello@nimply.io · https://nimply.io/contact

## License

MIT © Admire Digital Marketing FZC (Nimply)
