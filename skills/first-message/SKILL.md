---
name: first-message
description: Write to someone the workspace has never talked to — a new phone number on WhatsApp or SMS, a Telegram @username, an email address, a LinkedIn member, a Discord or X user. Use whenever messages.send answers "not found", or before writing to a number, handle or address that has no conversation yet.
---

# Write a first message

`recipient_name` only finds people who are already in contacts. A new person
is named by **where** they are, in `thread_id` (or one dedicated field), and
the workspace account that sends is named in `from_account_id`.

## Steps

1. **Check there is no conversation already.** `search.threads` / `contacts.find`
   with the number, handle or name. If one exists, reply there with its numeric
   `thread_id` and stop.
2. **Pick the sending account.** `channels.list(in_workspace=…)` lists the
   workspace's accounts; take the id of the one on the right channel. With
   several on a channel, ask which one when the request does not say.
3. **Send with the form for that channel** (international numbers, `+` and digits):

| To | Call |
|---|---|
| a phone number on WhatsApp (personal account, QR) | `messages.send(thread_id="whatsapp:+<number>", from_account_id=<wa account>, text=…)` |
| a phone number on WhatsApp Business API | `thread_id="whatsapp_business:+<number>"`; the first message must be a `template` |
| a phone number by SMS | `thread_id="sms:+<number>"`, from a phone number with SMS on |
| a Telegram @username | `recipient_username="@handle"` (no thread_id) |
| an email address | `messages.send_email(recipient_email=…, subject=…, text=…)` |
| a LinkedIn member | `thread_id="linkedin:member:<member id>"` |
| a Discord user | `thread_id="discord:<numeric user id>"` |
| an X user | `thread_id="x:@<handle>"` |
| a Skool member | `thread_id="skool:user:<user id>"` |
| a Teams user in the same company | `thread_id="teams:<email>"` |

4. **Read the answer.** It names the new conversation's numeric `thread_id`;
   use that for every following message, never the `whatsapp:+…` form again.

## Mistakes that fail

- A phone number in `recipient_name`: it is looked up as a contact's name and
  answers "not found". Put it in `thread_id` as above.
- `channel="whatsapp"` next to `recipient_name`: there is no such parameter,
  it is ignored.
- `to` / `body` instead of `recipient_email` / `text`: not parameters of
  `messages.send`; email goes through `messages.send_email`.
- A WhatsApp **group** id (`…@g.us`) cannot be opened this way; groups are
  created with `group.create`.

## Before sending

A first message to a stranger is outbound in the owner's name. Send it only
when the owner asked for it, from the account they named, and follow any
workspace rule about which accounts may write first.
