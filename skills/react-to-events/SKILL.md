---
name: react-to-events
description: Make something happen automatically when a message arrives — from one person, in one group, with a keyword, on one channel, or matching an intent. Covers notifying the owner, flagging a thread for a human, filing a task, and waking a Claude Code session. Use for "notify me when", "react automatically", "watch this chat", "tell me if X writes", "run this on every new lead", or when tempted to poll on a timer.
---

# React when something happens

Nothing here needs a polling loop. The workspace already knows when a message
arrives. The work is to say WHICH messages, and WHAT should happen.

## Steps

1. **Narrow the event before anything else.** "Notify me about messages" is
   the request; it is never the rule. Ask which person, which group, which
   words, which channel — and write the answer down as conditions:

   - one person's chat → the `thread_ids` parameter, after finding the chat
     with `search.threads` (or `contacts.find` for the person, whose profile
     names their threads). Its opposite is `excluded_thread_ids`, and an
     exclusion always wins.
   - never this sender, anywhere, calls included → `blocked_sender_ids` with
     a phone number, username or address. It beats every allow-list.
   - a group → context_types of dm, group, channel or livechat, plus
     group_mode set to mentions_only or questions. Without group_mode an
     agent in a busy chat answers everything.
   - words → keywords with keyword_match any or all. Free and deterministic:
     no model runs when it does not match.
   - meaning, when the words vary → ai_filter_ids. The one narrowing that
     costs money: the router embeds EVERY candidate message to compare it
     with the filter, whether or not anything ends up firing. Reach for it
     only when the wording genuinely varies, and pair it with a cheap
     condition (a channel, a folder, a chat type) so the paid comparison runs
     on a fraction of the traffic. Build and tune the filter with
     `ai_filters.create` and `ai_filters.test` first, and reuse an existing
     one by id instead of making a near-duplicate.
   - where it came from → channel_types, channel_account_ids, folder_ids,
     ai_tag_ids. A folder or a tag is how "only leads" is said without
     re-describing what a lead is.

2. **Decide what happens.** One of these, not several. Every one of them
   except the first two is a tool call, and an agent may only call what its
   allow-list names: a new agent's is EMPTY, so put the tool in
   `allowed_tools` with `agents.update` (or at creation) before expecting the
   reaction to happen. Without it the model has no such tool and writes
   something that looks like the call into its reply instead.

   - **answer the customer** — the ordinary case, an agent replies;
   - **a fixed reply with no model at all** — put the text in the trigger's
     template condition and a rule_based agent sends it verbatim, free and
     instant, substituting {from_name} and {message_text}. The cheapest right
     answer to "reply to every new lead with our hours". Add a handoff
     condition and a thinking agent runs behind the canned reply;
   - **tell the owner** — the agent calls `messages.send`, and on several
     channels nothing has to exist first: `recipient_username` resolves a
     Telegram handle and creates the contact and the thread, a bare number
     works on WhatsApp and Zalo, and email goes through
     `messages.send_email` with `recipient_email`. For someone already in
     contacts `recipient_name` is enough. Anywhere else it is a thread id, so
     have them write once from their personal account to a connected number
     and find that thread with `search.threads`.
     Set `from_account_id` when the workspace has several numbers and one of
     them must not be the sender;
   - **hand it to a person** — `job.escalate` flags the thread in the inbox
     and stops the agent talking over them;
   - **put it on the board** — `tasks.create` leaves the thread and the facts
     with someone, so nothing is lost when nobody is looking;
   - **wake a Claude Code session** — the advanced option below.

3. **Create the trigger** with `agents.trigger_create`. The narrowing from
   step 1 goes in `conditions`; scoping to particular chats goes in the
   `thread_ids` parameter beside it.

   ```
   agents.trigger_create(
       agent_id=<the agent that reacts>,
       trigger_type="incoming_message",
       thread_ids=[<the chat to watch>],
       conditions={
           "keywords": ["invoice", "payment"],
           "context_types": ["dm"],
           "cooldown_seconds": 60,
       },
       send_mode="draft",
   )
   ```

   `send_mode` draft means a person approves the reply before it goes out, and
   it is the right setting for step 2's first two options while the rule is
   new. It is the WRONG setting for a rule whose reaction is itself a message
   to somebody else: on draft, sending is intercepted, so the note to the
   owner becomes another draft waiting in the inbox and the rule reports
   success having told nobody. That rule wants auto from the start. Filing a
   task and escalating are not sends, and happen either way.

4. **Stop it becoming noise.** cooldown_seconds sets a minimum gap per
   conversation and max_runs_per_thread_per_hour a ceiling. once_per_thread
   makes the rule act once in a conversation and then step aside — right for
   a rule that files, tags or hands off, wrong for the agent that is holding
   the conversation.

5. **Prove it fires, and prove it stays quiet.** `agents.simulate_inbound`
   replays a message through the real router and answers which trigger won
   (with its conditions and why it was picked) or, on a miss, the reason it
   matched nothing. It drafts by default, so nobody is messaged: give it the
   `thread_id` the rule watches and one `message_text` that must match, then
   one that must not. A rule that never fires and a rule that fires on
   everything look identical from the outside until this is run.

   Two things to know about that run. It is a REAL run of the matched agent,
   so its non-sending work happens for real: simulating a rule that files a
   task files one, and simulating an escalation pauses the thread. And the
   simulator deliberately skips the trigger's rate limits so repeated tests
   do not trip them, which makes step 4's two caps the one part of the rule a
   simulation cannot check — send two matching messages for real, or trust
   them.

## Waking a Claude Code session (advanced)

A session can be the thing that reacts: the event reaches it, it reads the
threads, and it answers. Right when the reaction needs judgement across
several conversations rather than one reply.

There are two routes, and the cheap one is easy to miss:

- **Point a trigger straight at the session.** Make the agent with
  `agents.create`, text engine claude_channels (the "Claude Code" card in the
  New AI Agent dialog), and give it the trigger from step 3. It runs no model
  of its own, so this costs NOTHING inside the workspace: no intermediate
  agent, no tokens, the thinking happens in the session. It answers the
  conversation with `messages.send` and closes the run with
  `agents.task_complete`.
  Name the machine with `target_session` on `agents.trigger_create`, the way
  the desktop list spells it —
  without it the run is deliverable only while exactly ONE desktop is
  connected and refuses the moment a second window opens.

  Step 3's draft advice does not apply here. Nothing gates this route on the
  trigger's send mode, and the session replies over its own connection, which
  always delivers — so there is no draft to approve and the first live message
  reaches the customer. Try it on a chat of your own before pointing it at
  one.
- **Hand it a task** — the reacting agent ends its run with `tasks.create`
  naming the machine, and the session reports back with `tasks.comment`. Use
  it when several desktops are connected, or when the work wants a status and
  a history.

The allow-list from step 2 applies to the DialogBrain agent that hands the
work over, not to the session: that agent needs `tasks.create`, while the
session reaches the workspace over MCP with its own key and is not limited by
any agent's list.

**Check first.** Call `workspace.desktops`. If this machine is not in that
list, nothing in this section can reach you — use step 2's options instead.
Two things have to be true, and both are one-time setup:

- the channel needs an API key and a name for this machine, in
  `~/.claude/channels/dialogbrain/.env`;
- the session must have been STARTED with pushes enabled:
  `claude --channels plugin:dialogbrain@dialogbrain`. Without it Claude Code
  silently drops every push, and the only trace is a line in the session's own
  MCP log. Approval also needs the plugin allowlisted in managed settings
  (`channelsEnabled`, `allowedChannelPlugins`); a channel configured by hand
  instead of coming from the plugin is named `server:<name>` and needs
  `--dangerously-load-development-channels` as well. Every entry carries a
  tag — a bare name is refused before the session starts.

The flag is read at start, so switching it on means restarting the session.

The channel ships with this plugin and reads
`~/.claude/channels/dialogbrain/.env` once at start (an API key from the
cabinet under Settings → Developer and a stable name for this machine; the
server address comes from the plugin), so restart Claude Code after editing
it. That name is the address, and it matters because the gateway
hands an event to every connection the account has: with several sessions
connected and none named, the work is deliberately delivered to none rather
than to all of them.

| Error | What it means |
|---|---|
| `channel_not_connected` | No desktop registered. Usually the channel is not running, or points at a local server. |
| `channel_target_missing` | Several desktops, the task names none. |
| `channel_target_offline` | The name given is not among the live sessions. |
| `channel_target_ambiguous` | Two sessions share that name; target one by its session id. |

## The mistake to avoid

Reacting to everything. At a hundred conversations a day a rule on every
message produces a hundred identical notifications, and the one that mattered
is lost among them. Narrow first, then decide what happens.
