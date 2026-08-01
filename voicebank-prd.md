# PRD — Voice Bank (Build with Gemma NYC, On-Device Private Health Tools)

## Team
LittleRounds (pivoted) — Andrew Poveda + Maya

## One-liner
A voice-preservation tool for ALS patients: record your own voice saying key phrases while you still can, and later — when speech is gone — a rough typed or selected intent gets matched to the right recorded phrase and played back in your actual voice. Demoed as a simulated AR-glasses interface (stand-in for Meta Glasses hardware).

## What this is NOT
- Not voice cloning or speech synthesis — no AI-generated voice, only real recorded audio playback
- Not face modeling or video
- Not a prognosis or treatment tool — does not affect disease progression, only communication
- Not live QR-code camera scanning in the demo (too fragile on stage) — QR/scan visuals are decorative only, interaction is by tap

## Core concept
1. **Banking phase** (patient records now, while they can): record audio for a fixed list of common phrases
2. **Retrieval phase** (used later, when speech is difficult/gone): type or select a rough intent → Gemma4 matches it to the closest banked phrase → play that phrase's real recorded audio

## Tech stack
- Frontend: simple web app (Next.js or plain React — whatever Claude Code defaults to is fine, keep it lightweight)
- Audio: browser `MediaRecorder` API for recording, native `<audio>` for playback
- AI: local Gemma4 via Ollama (already installed, tested, confirmed working — `think: false` for speed)
- No backend/database required for the demo — in-memory or localStorage-equivalent state is fine (single demo session, not persistent across users)
- No auth, no real patient data, no HIPAA-grade anything — this is a demo, not production

## Build order (build and test each step before moving to the next)

### Step 1 — Audio record/playback loop
Single test page. One "Record" button:
- Tap to start recording (browser mic permission)
- Tap to stop
- Playback button appears, plays back what was just recorded
- Test this repeatedly (4-5 times) before moving on — this is the highest-risk piece (mic permissions, cross-browser quirks)

### Step 2 — Phrase bank UI
- Fixed list of 15-20 phrases across categories: needs (e.g. "I'm thirsty," "I'm in pain, get the nurse"), family/emotional (e.g. "I love you," "Tell [name] I'm proud of them"), general (e.g. "Yes," "No," "I'm tired")
- Each phrase has its own Record button (reuse Step 1 logic per item)
- Recorded audio is tied to its phrase text (so app knows "this clip = this phrase")
- Visual indicator (checkmark/color) for recorded vs. not-yet-recorded phrases

### Step 3 — Record real demo phrases
- Not a coding step — actually record all 15-20 phrases in one sitting, one consistent voice, quiet room
- This becomes the real demo data

### Step 4 — Matching layer (Gemma4)
- Text input: "Type what you want to say" (simulates rough/fragmented caregiver or patient input)
- On submit: send input text + the list of banked phrases to Gemma4 via Ollama
- Prompt pattern: given this rough intent, return the single closest matching phrase from the list, exactly as written, nothing else
- Look up the matched phrase's saved audio clip, play it
- Test with 5-6 varied inputs, confirm sensible matches

### Step 5 — "Glasses HUD" demo interface
- New screen styled as a simulated AR-glasses view: dark background, minimal centered UI, large tappable phrase-option buttons (2-3 shown at a time based on match/suggestions)
- Optional decorative QR-styled graphic (purely cosmetic — no live camera/scanning functionality)
- Tapping an option triggers the same match-and-play logic from Step 4

### Step 6 — Full flow integration
Landing screen → "simulate glasses view" → HUD screen → type or select intent → matched phrase plays in the recorded voice. Walk through the full sequence 3-4 times end to end.

### Step 7 — Polish + submission
- Simple intro/title screen explaining the concept for judges without context
- Test on the actual device used for the live demo (not just laptop browser)
- Submit via Kaggle — mentor check-in 3:00pm, submissions lock 3:45pm

## Success criteria for the demo
- Judge can watch: patient "banks" 2-3 phrases live or pre-recorded → caregiver types a rough intent → correct phrase plays back in the real recorded voice → all within a simulated glasses-style interface
- No live camera/scanning dependency
- No claims about prognosis, disease progression, or face/voice AI generation — messaging stays honest: preserves communication and voice, not the disease course

## Differentiation (for writeup/judge Q&A)
Existing voice banking tools (e.g. Team Gleason, ModelTalker) let patients record phrases but don't solve fast retrieval — as ALS progresses, motor control and time are extremely limited, and scrolling through long phrase lists is slow. This tool's differentiator is the semantic matching layer: go from a rough, low-effort intent straight to the right recorded phrase in one step.
