---
name: voice-agent
description: Build a phone / voice agent in DialogBrain — create it, set its greeting and voice, and test it by calling the owner. Use when someone asks for a phone agent, a voice bot, an agent that answers calls, or a call test.
---

# Build a voice agent

Long-form guide for humans: https://dialogbrain.com/help/voice-agent-mcp.
This is the tool sequence.

## Steps

1. **Collect**: what the agent says when it picks up, what it may book or
   promise, the language(s), and which number or channel it answers on (a
   Twilio/Telnyx number, a Telegram account, WhatsApp, a web widget).
2. **Create** with `agents.create` (`name`, `description`, `prompt_text` with
   the greeting as its first sentence, `send_mode` `"auto"` — a voice agent
   cannot draft, it speaks). Then `agents.update` with the voice settings:
   `agent_id`, `voice_greeting`, `voice_stt_language`, `voice_tts_language`,
   and `voice_tts_voice` / `voice_tts_provider` / `voice_stt_provider` only
   if the person named them; the defaults are fine.
3. **Number**: if no voice number is connected, use the `connect-channel`
   skill — voice carriers answer with a link that walks through buying or
   picking the number.
4. **Test by calling the owner**: `calls.make` (`phone_number` the person's own number, or `channel` `"telegram"` for a Telegram call, `voice_agent_id`, `instructions` for the test). Then `calls.wait` (`call_id`, `timeout_seconds` 300) and `calls.get_transcript` (`call_id`).
   Read the transcript back: did the greeting land, did it answer the test
   question, did it hang up politely?
5. **Fix and repeat**: `agents.update` (`agent_id`, `prompt_text`); call
   again. Two rounds are normal.

## Do not

- Do not call anyone but the person you are talking to for a test.
- Do not start outbound campaigns from here; that is a different, consented
  flow.
