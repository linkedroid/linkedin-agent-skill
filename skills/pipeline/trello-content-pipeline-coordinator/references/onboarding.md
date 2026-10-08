# Coordinator popup onboarding

Use `ask_user`. Put the recommended choice first and suffix its label with ` (Recommended)`. Ask one question at a time unless up to three answers naturally belong together. Every question needs 2-4 selectable options; free text is supplied automatically, so never add an Other option. For specific worker names, channel destinations, or schedule expressions, tell the operator to type them in the free-text response.

## 1. Run intent

Ask: **What do you want to do with the pipeline?**

- **Set up a new pipeline (Recommended)** - create the board, install workers, and schedule them.
- **Update an existing pipeline** - change workers, schedules, or contract fields.
- **Rebuild after a brief pivot** - archive the old brief, reset state, re-seed the Scout with the new brief.
- **Generate a status report only** - no changes.
- **Disable or re-enable workers** - toggle reliability state.

## 2. Notification route

Ask: **How do you want to be notified of new candidates?**

- **Webchat only (Recommended)** - jobs use `delivery: none`; no external channel required.
- **Telegram / Slack / Discord / email** - operator types the destination in free text; the coordinator validates the route is connected before scheduling.

## 3. Worker reliability policy

Ask only when at least one worker is currently disabled.

Ask: **Worker reliability policy?**

- **Keep current disabled workers**
- **Re-enable after a passing dry-run**
- **Disable specific workers by name** - operator types worker names in free text.

## 4. Pipeline contract fields

Ask only the missing fields: pipeline slug, board/workspace, destination platforms/accounts, watched profiles, schedules/timezones, per-run limits, creative provider/model, approval policy. Reuse stored values when present.

## 5. Run plan confirmation

Show the full plan in chat: intent, board/workspace, stages to create, enabled workers, notification route, schedules, creative providers, reliability policy. Then ask:

- **Use this plan (Recommended)**
- **Change answers**
- **Cancel run**

## 6. Rebuild confirmation (rebuild mode only)

When the operator chose `Rebuild after a brief pivot`, show the current brief fingerprint, the number of cards that will be archived, and the new brief fingerprint. Then ask:

- **Archive and rebuild (Recommended)**
- **Cancel rebuild**
