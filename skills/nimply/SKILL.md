---
name: nimply
description: Use when the user wants to plan, draft, schedule, publish, approve or measure social media posts, or edit their Nimply Pages link-in-bio page, through the Nimply MCP tools (list_channels, create_post, schedule_post, get_analytics, publish_link_page and friends).
---

# Working with Nimply

Nimply is a social media management workspace. The `nimply` MCP server exposes its public API as tools. Everything happens inside the one workspace the user authorized; publishing goes through Nimply's normal scheduling pipeline, so plan limits, approval rules and channel requirements apply exactly as in the app.

## Typical flow

1. `list_channels` to get channel ids, platform types and connection health. A channel with `tokenStatus: NEEDS_RECONNECT` cannot publish until the user reconnects it in the Nimply app.
2. Optionally `upload_media` (public URL) or `create_media_upload` + `complete_media_upload` (local file via presigned PUT) to get media ids.
3. `create_post` with the channel ids, content and media ids. One post is created per channel.
   - `schedule` omitted or `"draft"` saves a draft.
   - `"next_slot"` queues into each channel's next free posting slot (see `get_posting_schedule`).
   - an ISO 8601 UTC datetime publishes at that time.
   - `"now"` publishes immediately.
4. For YouTube, TikTok, Pinterest and LinkedIn prefer the platform tools: `create_youtube_video`, `create_tiktok_post` (call `get_tiktok_creator_info` first for allowed privacy levels), `create_pinterest_pin` (call `get_pinterest_boards` first), `create_linkedin_post`.

## Managing existing posts

- `list_posts` (filter by status or channel) and `get_post` to inspect.
- `update_post` edits a DRAFT or SCHEDULED post. Published posts cannot be edited.
- `schedule_post`, `unschedule_post`, `publish_post`, `delete_post` change state. Deletion is permanent.
- Approvals: `request_approval` moves a draft to PENDING_APPROVAL; `approve_post` / `reject_post` record the decision with an optional comment.

## Analytics

`get_analytics` with `scope="workspace"` (totals), `"channel"` + id (daily series) or `"post"` + id (snapshots). Dates are ISO 8601; default is the last 30 days.

## Link-in-bio pages (Nimply Pages)

`list_link_pages` → `get_link_page` (blocks in display order) → `add_link_block` / `update_link_block` edit the draft → `publish_link_page` makes it live at the page's nimply.link URL. Visitors never see unpublished changes, so always finish with `publish_link_page` when the user asked for the change to go live, and say so if you left it as a draft.

## Rules of thumb

- Default to drafts. Only schedule or publish when the user explicitly asks, and confirm before `publish_post`, `delete_post`, or anything with `schedule: "now"`. These actions reach public social networks and cannot be recalled.
- Publishing is asynchronous. After `publish_post` or `schedule: "now"` the post is SCHEDULED; poll `get_post` until it is PUBLISHED or FAILED and report the outcome.
- All timestamps are UTC. Convert from the user's timezone before scheduling, and say which time you used.
- Respect the permissions the user granted at connect time: read-only grants cannot create posts; `posts:publish` is required to publish.
- Never paste API keys into chat. The server authenticates with OAuth on first use; the API-key header is only for non-interactive setups.
