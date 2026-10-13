# ATLAS — Intelligence & Conversation Upgrade

This pack integrates the requested conversation/intelligence behavior without changing the
visual layer or voice stack.

## Changes
- Added `ConversationIntelligence` for lightweight local intent classification.
- AIManager now passes the detected intent into the existing system-prompt pipeline.
- PromptManager now enforces adaptive response length, concise follow-ups, contextual references,
  natural personality, two-layer answers, anti-repetition and a final quality check.
- Existing memory, provider, history, personality, privacy and plain-text/TTS constraints remain.
- No voice classes, TTS, STT or voice configuration were modified.
- No layouts, drawables, themes or existing app features were removed.
- Conversation history window was increased from 12 to 16 messages to preserve a little more
  short-term context without changing the architecture.

## Files changed
- `app/src/main/java/com/idcorporation/atlas/ai/AIManager.java`
- `app/src/main/java/com/idcorporation/atlas/ai/PromptManager.java`
- `app/src/main/java/com/idcorporation/atlas/ai/ConversationIntelligence.java` (new)
