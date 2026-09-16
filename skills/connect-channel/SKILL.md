---
name: connect-channel
description: Connect a messaging channel (WhatsApp, Telegram, a Telegram bot, email, Instagram, Facebook, LINE, Avito, a phone number…) to the DialogBrain workspace. Connects what needs no sign-in itself and gives the exact link for channels that need a QR scan or a browser sign-in. Use whenever someone asks to connect, add, link or hook up a channel or account.
---

# Connect a channel

One tool decides: `channels.connect`.

## Steps

1. **Ask the catalogue** if unsure of the channel id: `channels.connect`
   with no arguments returns every channel with its connect mode and link.
   Match the person's words to an id: `"whatsapp"`, `"telegram"` for a
   personal or business account, `"telegram_bot"` for a bot, `"email"`,
   `"instagram"`, `"facebook"`, `"line"`, `"avito"`, `"twilio_voice"`…
2. **Call `channels.connect` with just `channel`.**
   - Mode `"credentials"` → the result lists the fields. Ask the person for
     exactly those (say which are secret), then call `channels.connect`
     again with `channel`, `credentials` and a `display_name`. Email means
     IMAP/SMTP with an APP password — say so; the provider refuses a normal
     account password. The person must also give the IMAP and SMTP hosts
     (well known for major providers; ask otherwise). For Gmail prefer the
     link (Google sign-in) over IMAP.
   - If the error says the channel **needs the person** → the message
     already contains the link and what will happen there; paste the link
     as-is and say that one line. Do not describe where the button is in
     settings; the link opens the dialog directly.
   - If the error mentions **billing** → it contains the billing link; give
     it; nothing works until that is done.
   - If the error says **Permission denied** → the person is a viewer; a
     workspace owner or admin has to do this.
3. **Confirm it took**: `channels.get_profile` (`channel_account_id`) for the
   new account, or `search.threads` (`query`, `since`) once a first message
   arrives.
4. **Then route it**: a connected channel with no agent is just an inbox.
   Offer the `create-agent` skill.

## Do not

- Never ask for or relay a WhatsApp, Telegram, Instagram or Google password.
  Those channels are interactive on purpose: the sign-in must not travel
  through a chat. Give the link.
- Never say "I connected it" for a `"needs_person"` result. The person does
  the connecting; you gave the link.
- A website widget is not a channel to connect: use the
  `setup-livechat-widget` skill.
