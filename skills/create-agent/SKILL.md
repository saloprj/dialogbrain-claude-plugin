---
name: create-agent
description: Create a DialogBrain AI agent that answers customers on connected channels — from a business description to a working agent with instructions, tone, a trigger, and a verified test reply. Use when someone asks for an assistant, a bot, an auto-reply, an agent, or "something that answers my customers".
---

# Create an agent that answers customers

## Steps

1. **Collect, in one message**: what the business does, who writes in and
   about what, the tone (formal / friendly), the languages, what the agent
   must NOT do (prices it may not quote, promises it may not make), and when
   a human takes over. Do not start until you have the "must not" list.
2. **Check the channels**: the agent answers wherever its trigger fires, so
   the channel must be connected first (`connect-channel` skill).
   `agents.list` (`status` `"active"`) shows what already exists.
3. **Create** with `agents.create`: `name`, `description`, `prompt_text`
   written FROM the business TO the customer in the collected tone, and
   `send_mode` `"draft"` so a person approves each reply for the first days
   (`"auto"` only when the person says so). Put the "must not" list and the
   hand-over rule in the prompt as explicit sentences. A `template` is a
   starting point only if the person named one.
4. **Add the trigger** with `agents.trigger_create`: `agent_id`,
   `trigger_type` `"new_message"`, `send_mode` `"draft"`, and `conditions`
   only if the person wants a subset (a channel, a keyword).
5. **Test without a real customer**: `agents.simulate_inbound` with
   `channel_account_id`, a realistic `message_text`, `send_mode` `"draft"`.
   Then `agents.traces_list` (`agent_id`, `limit` 1) and `agents.trace_get`
   (`trace_id`, `full` true): read what the agent actually answered and which
   tools it called. Show the reply to the person and ask whether that is
   what they wanted. Iterate with `agents.update` (`agent_id`, `prompt_text`)
   until it is.
6. **Say what happens next**: replies land as drafts in the inbox until
   `send_mode` is switched; escalations go to a person.

## Do not

- Do not write the prompt in the second person about the agent ("you are a
  helpful assistant"). Write what the business says to its customers.
- Do not switch `send_mode` to `"auto"` without being told.
- Do not put example data from one business into another's prompt.
