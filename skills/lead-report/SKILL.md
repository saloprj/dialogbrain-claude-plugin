---
name: lead-report
description: Answer "where are we losing leads" for a DialogBrain workspace — unanswered conversations, slow first replies, drop-offs by channel, with the actual threads behind the numbers and one written summary. Use for weekly reviews, funnel questions, or "how did support do this week".
---

# Where leads are lost

## Steps

1. **Fix the window** (default: the last 7 days) and the channels in scope.
2. **Numbers first** with `analytics.query` (`question`): conversations
   started, share with no reply from the business, median time to first
   reply, conversations with no message from the business in the last 48 h —
   per channel. Ask for one table.
3. **Then the threads**: `search.threads` (`query`, `only_unanswered` true, `since` set to the window start, `limit` 20); `search.messages` (`query`, `date_from`, `date_to`) inside a thread when a number looks wrong. Read
   three real examples per problem, not just the count.
4. **One summary**, written for the owner, not the analyst: what is lost,
   where, one sentence each, and the single change that would recover the
   most. Save it with `artifacts.create` (`title`, `html`, `access_level`
   `"workspace"`) so it has a link, and paste the link.
5. **Offer the fix as an action**: an agent for the unanswered channel
   (`create-agent` skill), or a task for the person who owns it
   (`tasks-board` skill).

## Do not

- Do not report a percentage without the count behind it.
- Do not name a customer in the summary; name the channel and the pattern.
