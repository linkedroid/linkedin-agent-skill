# Publisher popup onboarding

Use `ask_user`. Put the recommended choice first and suffix its label with ` (Recommended)`. Ask one question at a time unless up to three answers naturally belong together. Every question needs 2–4 selectable options; free text is supplied automatically, so never add an Other option. For specific card titles, ISO 8601 timestamps, or account ids, tell the operator to type them in the free-text response.

## 1. Run scope

Ask: **What should I publish this run?**

- **Publish every publishable card in Approved (Recommended)** — every card whose approval fingerprint matches, whose `Publish status` is not `published` or `scheduled`, and whose destination is unambiguous.
- **Publish one selected card** — the operator types the card title or short link in free text.
- **Schedule every card with a `Scheduled for:` field** — only schedule; do not publish anything immediately.
- **Hold every card** — do nothing this run.

## 2. Override placeholder warnings

Ask only when the contract has `block_mock_testimonials: true` and any selected card contains a flagged testimonial or unverified claim pattern.

Ask: **Override placeholder warnings?**

- **Hold and surface the warnings to me (Recommended)**
- **Publish anyway** — the operator types `yes` in free text to confirm.

## 3. Run plan confirmation

Show the full plan in chat: scope, eligible card titles, destination account ids, intended publish or schedule times, upload path, blocked warnings. Then ask:

- **Use this plan (Recommended)**
- **Change answers**
- **Cancel run**

## 4. Per-card intent

For each card, render the exact final copy, `MEDIA:<asset path>`, destination account id and type, intended time, and any platform notice. Then ask:

- **Publish now (Recommended)**
- **Schedule for:** — the operator types an ISO 8601 timestamp with timezone in free text.
- **Hold — return to Approved, do not publish**

The operator must type the chosen option in the same turn. An `Approved` Trello list is not sufficient authorization.
