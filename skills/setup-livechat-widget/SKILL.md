---
name: setup-livechat-widget
description: Put a DialogBrain chat widget on a website — create it, restrict it to the site's domains, hand over the two script tags, and make sure an agent answers. Use when someone asks for a chat widget, live chat, a chat bubble on their site, or "how do I put DialogBrain on my website".
---

# Set up a LiveChat widget

A widget is one installation: its own key, its own look, its own allowed
domains. Most businesses need exactly one.

## Steps

1. **Ask for two things before creating anything**: the site's domain (with
   and without `www` — they count as different) and the name to show in the
   panel header. Everything else has good defaults.
2. **Create it** with `widgets.create`: `name`, `primary_color`, `position`
   (`"bottom-right"` unless told otherwise), `header_title`, `display_mode`
   (`"chat"`), `auto_reply_mode` (`"draft"` so a person approves the first
   replies).
3. **Restrict and polish** with `widgets.update`: `widget_id`,
   `allowed_domains` (the list from step 1), `header_subtitle` (an honest
   expectation such as "we reply within minutes"), `greeting_text`.
4. **Get the snippet** with `widgets.get_embed_code` (`widget_id`) and show
   it verbatim. It is two script tags; tell the person to paste them before
   `</body>` on every page, or into their tag manager. Platform notes:
   https://dialogbrain.com/help/livechat-widget (#wordpress, #shopify, #wix).
5. **Make sure someone answers.** `agents.list` (`status` `"active"`) — if no
   agent is set up to answer web chat, say so plainly and offer the
   `create-agent` skill. A widget with no agent collects conversations nobody
   answers.

## Do not

- Do not invent a widget key or a script URL. The snippet comes from
  `widgets.get_embed_code` only.
- Do not leave `allowed_domains` empty on a real site: the key is public and
  the domain list is what stops someone else spending this agent.
