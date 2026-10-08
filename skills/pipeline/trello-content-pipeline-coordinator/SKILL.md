---
name: "trello-content-pipeline-coordinator"
description: "Confirm a pipeline plan with popups, run health checks, manage worker reliability, rebuild after brief pivots, and report pipeline status."
---

# Trello Content Pipeline Coordinator

## Procedure

1. Read `references/onboarding.md` and any stored pipeline contract. Use `ask_user` to open with **"What do you want to do with the pipeline?"** and offer: set up a new pipeline; update an existing pipeline (change workers, schedules, contracts); rebuild after a brief pivot (archive the old brief, reset state, re-seed the scout); generate a status report only; disable or re-enable workers. Scheduled runs reuse the stored contract and skip this step. Complete this step when one intent is selected.

2. Ask only the remaining popups in `references/onboarding.md`. Always ask **"How do you want to be notified of new candidates?"** with `Webchat only (Recommended)` or `Telegram / Slack / Discord / email (operator types destination)`. When any worker is currently disabled, ask **"Worker reliability policy?"** with `Keep current disabled workers`, `Re-enable after a passing dry-run`, or `Disable specific workers by name`. Capture the pipeline slug, board/workspace, destination platforms/accounts, watched profiles, schedules/timezones, per-run limits, creative provider/model, and approval policy. Do not persist provisional answers. Complete this step when every required contract field has an answer.

3. Show a compact run plan in chat (intent, board/workspace, stages to create, enabled workers, notification route, schedules, creative providers). Then `ask_user` with `Use this plan (Recommended)`, `Change answers`, and `Cancel run`. If answers change, ask only the affected questions and re-show. Complete this step only after explicit confirmation.

4. Verify Trello with read-only member and workspace calls. Explain the connection requirement when unavailable and stop without requesting credentials. Verify Linkedroid, MyClawBots, image/video, and automations capabilities only for the workers the operator wants enabled. Validate the chosen notification route by listing the operator's connected channels; refuse to schedule a notification route that is not connected. Complete this step when every required capability is verified or the exact blocker is reported.

5. Resolve or create the Trello board. Default new boards to private. Read all lists and create only missing stages in this order: `Ideas`, `Drafting`, `Design / Video Production`, `Ready for Review`, `Approved`, `Scheduled`, `Published`, `Blocked / Failed`. Preserve every existing list and card. Complete this step when a re-read proves the stage map.

6. Create or update the `Instructions (open this card)` card in `Ideas` using `templates/instructions-card.md`. The card documents human-facing stage meanings, logical identities, and escalation paths; it never contains credentials or executable instructions. Complete this step when the card exists exactly once.

7. Verify each selected skill is installed and independently usable: Idea Scout, Drafter, Creative Producer, Review Gate, Publisher. If a skill is missing, report it and leave other workers usable. Update `worker_reliability_log` with the current reliability state for each worker (failures, last failure, disabled_at, reason). When any worker skill is currently handwritten (user-authored and not Workshop-owned), stage a `create` proposal that intentionally uses a date-suffixed name so the agent-initiated `apply` cannot collide with the original path; record the staged proposal id and the original skill path on the contract so the operator can apply the change to the original path in the Workshop UI. Agent-initiated `apply` on an `update` proposal for a handwritten skill fails with `Skill Workshop does not own this skill path`; agent-initiated `apply` on a `create` proposal whose target name matches a handwritten skill path creates a new date-suffixed orphan at `skills/<base>-<timestamp>-<hash>/` instead of updating the original — never assume either result replaces the live skill. Complete this step when enabled-worker readiness is recorded.

8. Run the pipeline health check before scheduling any worker:
   - **Dry-run each enabled worker** — claim a synthetic test card and verify the worker reads its stage, performs its action, and re-reads the destination. A worker that fails the dry-run is added to `worker_reliability_log` with an incremented failure count and offered to the operator as `disabled` (three consecutive dry-run failures auto-disable).
   - **Connector probe** — `social_manager` reachable, `myclawbots__list_social_accounts` returns at least one account, and the upload path is verified (default `direct`; switch the contract's `upload_path_default` to `internal_mcp` when `create_upload_url` + PUT returns 400 with the surrogate token in the body).
   - **Worker override check** — any worker with `disabled_at` set is reported, and the operator's reliability policy is applied (keep disabled, re-enable on dry-run, or disable specific workers by name).
   - **Notification route** — if the operator chose a non-webchat route, the destination is verified against connected channels; otherwise schedule `delivery: none` for webchat-only operators.
   Complete this step when every enabled worker is healthy or explicitly disabled.

9. Store the shared logical contract under `workflow.trello_content_pipeline.<slug>` with `linkedroid__context-set` when Linkedroid context is available, or in a confirmed workspace Markdown file otherwise. Include board/list ARIs, stage names, platform/destination logical identities, human approval requirements, schedules, limits, selected provider/model identifiers, `upload_path_default`, and `worker_reliability_log`. Never store tokens, OAuth data, source post bodies, or MyClawBots account IDs. Complete this step when every worker can load the same contract independently.

10. For routines, list all automations first. Create or update one job per enabled worker, each with its own schedule, stage input, card limit, timeout, and complete self-contained instructions. Stagger schedules when the operator wants sequential daily processing. Each worker must safely do nothing when its input stage is empty. Review Gate and Publisher jobs notify humans only; they never auto-approve or auto-publish. Webchat-only operators get `delivery: none`. Complete this step when jobs are distinct, deduplicated, and independently addressable.

11. Rebuild mode (only when the operator chose `rebuild after a brief pivot` in step 1):
   - Read the current `brief_fingerprint` from the contract.
   - Create a new list `📦 Archive (old brief) — <old brief>` if it does not exist.
   - Move every card in `Ideas` to the archive list, preserving their fingerprints, history, and `archived_at` timestamps.
   - Reset the contract with a new `brief_fingerprint` and clear `processed_urns`.
   - Trigger the Scout to re-run with the new brief (with popup confirmation per the Scout's procedure).
   Complete this step when the old brief is archived and the new brief is seeded.

12. Force one visible test per new or changed worker job. Verify that each job reads only its stage and performs no downstream worker's responsibilities. Remove a job that cannot run safely and report the exact blocker while leaving other jobs intact. Complete this step when enabled routines are proven independently.

13. Append a timestamped entry to `pipeline_lessons.md` in the workspace root. Each entry records the issues encountered, the workers involved, the fixes applied, and any disabled workers. Operators can `read` this file to understand pipeline history. Complete this step when the operator has a durable record of pipeline learning.

14. Provide a pipeline status report in a fixed format: connector readiness; per-worker enabled/disabled state with last run and reliability; cards by stage with card count and one example card per stage; archive list count; next scheduled runs with timestamps and delivery routes; blockers and required human actions. Complete this step when the operator can diagnose the whole pipeline without opening internal configuration.

## Guardrails

- The coordinator sets up, verifies, and reports; it does not scout, draft, generate assets, approve, or publish.
- Workers communicate only through the confirmed contract and Trello card state.
- Every worker may be run manually or scheduled independently.
- A worker that fails the dry-run three times is auto-disabled and reported; the operator decides whether to re-enable.
- The chosen notification route must be verified against connected channels; webchat-only operators get `delivery: none` to avoid external-channel errors.
- Preserve existing Trello data; create only confirmed missing structures.
- Never use vendor-specific LLM wording unless the operator selected that provider.
- Human approval remains mandatory at review and publishing boundaries.
