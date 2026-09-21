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

## Letting DialogBrain reach you (not available to everyone yet)

The plugin also ships a channel server, so a DialogBrain agent can hand work
to this session instead of answering by itself — a new lead worth judging, a
task assigned from a phone.

**Read this before trying.** Inbound push into a Claude Code session is an
experimental feature of Claude Code, and two of its gates are not ours to
open:

- the Channels feature has to be enabled for your account;
- the channel has to be approved. Approval comes from an allowlist Claude Code
  itself carries, or from your organisation's managed settings. A plugin that
  is on neither is refused unless the session is started with
  `--dangerously-load-development-channels`, which is meant for local
  development.

So installing the plugin does NOT give you this. It gives you the tools, the
skills and the agents, which need no flags at all. If the steps below end with
Claude Code saying the channel "is not on the approved channels allowlist",
that is this gate, not a mistake on your side, and nothing in the plugin can
lift it.

Nothing happens until you set it up either: with no API key the channel stays
off, the session is unaffected, and the plugin's tools work as before.

It needs **Node.js 20 or newer on PATH** — Claude Code installed as a
standalone binary does not bring one. Without it the channel never starts,
and the only evidence is a spawn error in the session's own MCP log, so check
`node --version` first.

1. Put an API key (cabinet → Settings → Developer) and a name for this machine
   in `~/.claude/channels/dialogbrain/.env`:

   ```
   DIALOGBRAIN_TOKEN=<your key>
   DIALOGBRAIN_CHANNEL_NAME=<a name you will recognise>
   ```

2. Start Claude Code with pushes enabled — inbound push is off by default and
   a running session cannot be switched on:

   ```
   claude --channels plugin:dialogbrain@dialogbrain
   ```

   On a managed machine the pair must also be approved once, in
   `/etc/claude-code/managed-settings.json`:

   ```json
   { "channelsEnabled": true,
     "allowedChannelPlugins": [{ "plugin": "dialogbrain", "marketplace": "dialogbrain" }] }
   ```

3. Ask for it in words: "wake me here when a new lead arrives". The
   `react-to-events` skill sets the rest up.

Name the channel in ONE flag only. Listing it in both `--channels` and
`--dangerously-load-development-channels` registers it twice — once without
the development mark — and the check trips over the unmarked copy and refuses
the lot.

If nothing arrives, ask DialogBrain which sessions it can see. Being in that
list proves only that the channel server reached the server: it opens its
socket whether or not Claude Code is willing to deliver anything. The session's
own MCP log is the honest answer — it says either "Channel notifications
registered" or the reason it skipped them.

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
