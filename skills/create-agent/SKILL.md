---
name: create-agent
description: Create a DialogBrain AI agent that answers customers on connected channels — from a business description to a working agent with a knowledge base, instructions, tone, a trigger, and a reply verified against the facts. Use when someone asks for an assistant, a bot, an auto-reply, an agent, or "something that answers my customers".
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
3. **Build the knowledge base before the agent.** An agent with nothing to
   read answers from the model's imagination. If the facts live on a
   website, fetch its pages (the sitemap, then each public page), keep the
   main text of each, and put them into ONE markdown file with a heading
   per page and its URL. Upload it: `files.upload` (`content` base64,
   `filename`, `mime_type` `"text/markdown"`) up to about 50 KB, otherwise
   `files.create_upload_url` → PUT → `files.complete_upload`. Then
   `collections.create` (`name`), `collections.add_file`
   (`collection_id`, `file_id`). Documents the person hands you go in the
   same way. Indexing runs in the background: poll `collections.list_files`
   (`collection_id`) until every file shows `status` `"ready"` and a
   `chunk_count` above zero before anything is tested — an agent asked too
   early answers from an empty index, which looks exactly like a broken
   setup. Say what got indexed and what did not.
4. **Create** with `agents.create`:
   - `name`, `description`, `prompt_text` written FROM the business TO the
     customer in the collected tone. Put the "must not" list and the
     hand-over rule in the prompt as explicit sentences.
   - `send_mode` `"draft"` so a person approves each reply for the first
     days (`"auto"` only when the person says so).
   - `allowed_tools` `["knowledge.query"]` at minimum. A new agent starts
     with NO tools; without this it cannot read the knowledge base and
     imitates the search in its reply text instead.
   - `model`: pick one with native tool calling, `"gpt-4.1-mini"` unless
     the person names another. The platform default has been seen printing
     raw tool-call markup into replies, and Anthropic models fail with
     "AI service authentication failed" in a workspace that has no key for
     them. If the first test fails that way, switch the model with
     `agents.update` rather than rewriting the prompt.
   - The hand-over rule is words in the reply ("a person will get back to
     you, how can we reach you?"), not a tool: with this allow-list the
     agent has no `agent.handoff` or `job.escalate`, and it does not need
     them while replies are drafts a person reads anyway.
   - A `template` is a starting point only if the person named one.
   Then `collections.assign_agent` (`collection_id`, `agent_id`).
5. **Add the trigger** with `agents.trigger_create`: `agent_id`,
   `trigger_type` `"incoming_message"`, `send_mode` `"draft"`, and
   `conditions` only if the person wants a subset — for a website chat,
   `{"channel_types": ["livechat"]}`.
6. **Test against the facts, not the tone.** Take three concrete facts out
   of the knowledge base (a time, a price, a count, a name) and ask for them
   with `agents.simulate_inbound`: the first call with `create_new_livechat`
   true, every later call with the `thread_id` that first call returned, so
   the run leaves ONE test thread, not five. On that thread, and only there,
   use `send_mode` `"auto"` so each reply is persisted instead of being
   overwritten by the next draft: `"auto"` delivers through the real
   channel, so a thread that belongs to a real person would receive the
   test reply. The agent's own `send_mode` stays `"draft"`; the simulation
   overrides it for these runs only. Add one message that must
   trigger the hand-over wording and one that pokes a "must not". Then
   `agents.traces_list` (`agent_id`, `limit` 1) and `agents.trace_get`
   (`trace_id`, `full` true) for each: the reply must quote the fact as the
   document has it, and the trace must show a `knowledge.query` call with no
   invented `file_ids`. "I'm not sure", a figure that is not in the
   document, or a tool call typed out as text all mean the setup is broken,
   not the prompt — check the model, the allowed tools and the collection
   before touching the wording. Show the person the replies and the facts
   side by side, and tell them the test thread is theirs to delete.
7. **Say what happens next**: replies land as drafts in the inbox until
   `send_mode` is switched; escalations go to a person; new pages on the
   site are not picked up until the file is re-uploaded.

## Do not

- Do not write the prompt in the second person about the agent ("you are a
  helpful assistant"). Write what the business says to its customers.
- Do not switch `send_mode` to `"auto"` without being told.
- Do not put example data from one business into another's prompt.
- Do not pass ids you have not seen in this workspace — a `file_id` or
  `thread_id` copied from a tool description matches nothing, the search
  comes back empty, and the agent starts guessing.
- Do not simulate with `send_mode` `"auto"` on any thread other than the
  one `create_new_livechat` returned: a real person's thread gets the reply.
- Do not call the agent finished on a reply that merely sounds right.
