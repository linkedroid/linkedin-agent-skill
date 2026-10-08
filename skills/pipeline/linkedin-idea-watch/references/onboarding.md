# Popup onboarding matrix

Use `ask_user`. Put the recommended choice first and suffix its label with ` (Recommended)`. Ask one question at a time unless up to three answers naturally belong together. Every question needs 2–4 selectable options; free text is supplied automatically, so never add an Other option. If exact URLs, notes, or wording are required, tell the operator to type them in the free-text response.

## 1. Planning mode

Ask: **How do you want to plan your content?**

- **Draft from my instructions (Recommended)** — start from the operator’s topic, offer, experience, or notes.
- **Watch LinkedIn profiles** — monitor supplied profiles and extract content signals.
- **Watch + adapt to my ICP/voice** — use profile signals, then create original ideas for the operator’s audience and voice.
- **Use another source** — article, transcript, document, notes, or URL.

## 2. Branch questions

### Instructions

1. **Goal/topic:** Build authority; generate leads; educate the audience; promote an offer. Type the exact topic in free text when these are too broad.
2. **Starting material:** No source; use my notes; refresh my own draft/post; use supplied links/documents.
3. **Drafting direction:** Original in my voice; closely refresh my own material; research summary with attribution.

### LinkedIn profiles

1. **Profiles:** Use saved profiles; use my own profile; type new LinkedIn URLs in free text.
2. **Ownership:** My profiles/content; third-party profiles; mixed.
3. **Use policy:** Original ideas in my voice; adapt mechanics for my ICP; attributed research summary. Offer close refresh only when ownership is the operator’s.
4. **Scope:** New posts only; rolling recent window; one selected post. Collect count and maximum age when not already stored.

### Another source

1. **Source type:** Article/URL; transcript/video notes; my document/notes; my existing draft. Request the exact URL or text in free text.
2. **Ownership:** Mine; third-party; mixed.
3. **Use policy:** Original ideas in my voice; adapt for my ICP; attributed research summary; close refresh only for owned material.

## 3. Shared questions

Ask only missing fields:

- **Audience/ICP:** Use saved ICP; B2B SaaS founders; B2B marketers; B2B sales teams. Free text may define another audience.
- **Voice:** Use saved voice; concise operator; educational expert; personal founder story. Free text may specify style rules.
- **Restrictions:** Use saved restrictions; no unsupported performance claims; no testimonials; type custom prohibited topics/claims.
- **Formats** (multi-select allowed): Text; text + one image; carousel; video. Respect a stored single-image policy unless the operator explicitly overrides it for this batch.
- **Idea count:** 3; 5; 10. Free text may request another count.
- **Delivery:** Trello Ideas + chat summary; chat preview only; both full cards in chat and Trello.
- **Cadence:** One-time; on demand; scheduled routine. For a routine, collect active days, local time, IANA timezone, new-only/rolling scope, notification route, and volume cap.

## 4. Confirmation

Show the full plan in chat: mode, sources, ownership, objective, ICP, voice, source-use policy, restrictions, formats, count, delivery, and cadence. Then ask:

- **Use this plan (Recommended)**
- **Change answers**
- **Cancel setup**

## 5. Idea decision

After generating the preview, ask:

- **Save all ideas (Recommended)**
- **Revise ideas**
- **Select ideas**
- **Keep in chat only**

Do not create Trello cards until the approved set is explicit.
