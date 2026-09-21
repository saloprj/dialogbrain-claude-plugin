# DialogBrain for Claude Code

Connect WhatsApp, Telegram, email and a chat on your website to one inbox, and
build AI agents that answer your customers there — all from Claude Code.

DialogBrain is a customer messaging platform: every channel lands in one shared
inbox, with contacts, calls, a task board and AI agents on top. This plugin puts
that workspace inside Claude Code as tools, plus six skills that know the order
of each setup job.

## Install

    claude plugin marketplace add saloprj/dialogbrain-claude-plugin
    claude plugin install dialogbrain@dialogbrain

Then run `/mcp` inside Claude Code and sign in to DialogBrain in the browser.
There is no API key to copy, and the plugin can do exactly what your own account
can do, in the workspace you are signed in to.

## Things to ask for

    Connect my WhatsApp and my work email.

    Put a chat widget on example.com. Only allow my domain
    and keep replies as drafts I approve.

    Create an agent that answers customers on WhatsApp and
    the website chat. Never promise prices. Test it before
    it answers anyone.

    Where did we lose leads this week?

## What the skills do

Ask in plain words; the matching skill loads on its own, and only then.

- **connect-channel** — "connect my WhatsApp / my email / a Telegram bot":
  connects what needs no sign-in (an IMAP mailbox, a bot token) straight from the
  chat, and for the rest sends you a link that opens the exact screen.
- **setup-livechat-widget** — "put a chat widget on my site": creates the widget,
  restricts it to your domains, hands you the two script tags, and checks that an
  agent answers.
- **create-agent** — "an agent that answers my customers": writes it from a
  description, wires a trigger, sends a test message and reads the trace.
- **voice-agent** — "a phone agent": creates it, sets the voice, and calls you to
  test it.
- **tasks-board** — the workspace task board: assign work to people or agents,
  track what is overdue, comment with screenshots.
- **lead-report** — "where did we lose leads this week": queries analytics, reads
  the threads, and writes one summary.
- **react-to-events** — "tell me when this person writes", "watch this group",
  "notify me on every new lead": narrows the trigger to the messages you care
  about, then picks what happens — a reply, a note to you, a human takeover, a
  task on the board.

## Channels it can connect

WhatsApp, Telegram, a Telegram bot, Instagram, Facebook Messenger, email over
IMAP or Google, a chat widget on your website, and phone numbers for voice calls.

## What it will not do

Connecting WhatsApp means scanning a QR code with the phone that owns the
number, the way WhatsApp Web works. No assistant should do that for you, so this
one does not try: it sends you a link that opens DialogBrain on that step. For a
mailbox it asks for an app password, never your main password, and you can revoke
it at any time. An agent's replies wait as drafts until you decide it can answer
on its own.

## Just the server, no skills

    claude mcp add --transport http dialogbrain https://api.dialogbrain.com/mcp/

The same address works in ChatGPT, Claude, Cursor, Codex, Gemini CLI and any
other MCP client, with OAuth sign-in:
https://dialogbrain.com/help/set-up-from-ai-assistant

## Requirements

A DialogBrain account (dialogbrain.com) and Claude Code. New workspaces get a
free trial, so you can connect a channel and test an agent before paying.

MIT licensed. This repository is published from the DialogBrain monorepo on
every release; changes are made there, not here.
