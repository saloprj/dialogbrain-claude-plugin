---
name: tasks-board
description: Run the DialogBrain workspace task board — create and assign tasks to people or agents, track status and overdue work, comment with evidence. Use when asked to create, find, update, assign or report on tasks or to-dos.
---

# The workspace task board

The board belongs to the whole workspace, not to one agent.

## Tools

- `workspace.members` (`include_agents` true) — who can be assigned. Call
  before guessing a name.
- `tasks.create` — `title`, `description`, `assignee`, `due_at`, `priority`,
  `attachment_file_ids`. No assignee = backlog.
- `tasks.list` — filters `assignee`, `status`, `overdue`, `unassigned`,
  `created_by_me`.
- `tasks.update` — `task_id` with `status`, `priority`, `due_at`,
  `assignee`. `status` `"done"` completes.
- `tasks.comment` — `task_id`, `body`, `attachment_file_ids` for progress,
  questions, evidence.

## Rules

- **Assignee by name**: `assignee` takes an email, a username, an agent name
  or `"me"`. An ambiguous name returns the candidates — pick one, never retry
  with a guess.
- **Never report a bare id.** Every task carries the assignee's and the
  creator's names; say "task 41, assigned to Support Three".
- **Assigning to an agent wakes it**: the agent starts on the task at once.
  Say so when you assign.
- **Screenshots are viewable**: `files.upload` (`content`, `filename`,
  `mime_type`) then the returned id in `attachment_file_ids`;
  `files.get_base64` (`file_ids`) returns the image itself, so look rather
  than describe.
- **A viewer has no access.** A permission error for a viewer is correct.
