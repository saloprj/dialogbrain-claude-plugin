# DialogBrain for Claude Code

Your DialogBrain workspace — inbox, contacts, calls, agents, tasks, knowledge —
as tools inside Claude Code, plus skills that know how to set things up.

## Install

    claude plugin marketplace add saloprj/dialogbrain-claude-plugin
    claude plugin install dialogbrain@dialogbrain

Then, inside Claude Code, run `/mcp` and sign in to DialogBrain in the browser.
No API key to copy. Access follows your account and its current workspace.

## What the skills do

Ask in plain words; the matching skill loads on its own.

- **setup-livechat-widget** — "put a chat widget on my site": creates the widget,
  restricts it to your domains, hands you the two script tags, checks an agent answers.
- **connect-channel** — "connect my WhatsApp / email / Telegram bot": connects
  what needs no sign-in (IMAP email, a bot token, Avito) and gives you the exact
  link for the rest.
- **create-agent** — "an agent that answers my customers": builds it from a
  description, wires a trigger, sends a test message and reads the trace.
- **voice-agent** — "a phone agent": creates it, sets the voice, and calls you to test.
- **tasks-board** — the workspace task board: assign to people or agents, track
  overdue, comment with screenshots.
- **lead-report** — "where did we lose leads this week": queries analytics,
  reads the threads, writes one summary.

## Just the server, no skills

    claude mcp add --transport http dialogbrain https://api.dialogbrain.com/mcp/

Other clients (Codex, Cursor, Gemini CLI, Claude.ai, ChatGPT): https://dialogbrain.com/help/mcp

This repository is published from the DialogBrain monorepo on every release;
changes are made there, not here.
