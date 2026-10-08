# Modular content pipeline

## Independent workers

| Worker | Reads | Writes/moves to | Never does |
|---|---|---|---|
| Idea Scout | LinkedIn profiles/posts, non-LinkedIn sources | `Ideas` | final drafting, media, approval, posting |
| Content Drafter | `Ideas` | `Drafting`, then `Design / Video Production` or `Ready for Review` | final media, approval, posting |
| Creative Producer | `Design / Video Production` | `Ready for Review` | copy rewrite, approval, posting |
| Review Gate | `Ready for Review` | `Approved`, revision stages, or `Blocked / Failed` | posting |
| Social Publisher | `Approved` | `Scheduled`, `Published`, or `Blocked / Failed` | rewriting or approval inference |
| Coordinator | pipeline configuration and status | board/contract/automations | all worker content actions |

Every worker is provider-neutral and uses the model/runtime configured by the operator. Creative models and publishing connectors are explicit capabilities, not assumptions about the LLM.

## Worker reliability log

The contract carries a `worker_reliability_log` field. Each entry records:

- `failures`: consecutive failure count
- `last_failure`: ISO timestamp
- `disabled_at`: ISO timestamp or null
- `reason`: short human reason

A worker that fails the dry-run three times is auto-disabled. The operator decides whether to re-enable. Disabled workers are never scheduled; they appear in the pipeline status report as `disabled` with the reason.

## Notification route

- Webchat-only operators use `delivery: none` for every job. No external channel errors.
- Operators with a connected channel (Telegram, Slack, Discord, email) use the matched channel; the coordinator validates the route is connected before scheduling.

## Rebuild after a brief pivot

- Read the current `brief_fingerprint` from the contract.
- Create a new list `📦 Archive (old brief) - <old brief>` if it does not exist.
- Move every card in `Ideas` to the archive list, preserving their fingerprints, history, and `archived_at` timestamps.
- Reset the contract with a new `brief_fingerprint` and clear `processed_urns`.
- Trigger the Scout to re-run with the new brief (with popup confirmation per the Scout's procedure).

## Pipeline lessons log

The coordinator appends a timestamped entry to `pipeline_lessons.md` in the workspace root after every run. Each entry records the issues encountered, the workers involved, the fixes applied, and any disabled workers. Operators can `read` the file to understand pipeline history.

## Optional schedule example

Use only after operator confirmation:

- Idea Scout: morning
- Drafter: after the scout window
- Creative Producer: after drafting
- Review Gate: human notification window
- Publisher: checks Approved cards or executes approved exact-time jobs

These are separate jobs. Empty input stages result in a quiet no-op.
