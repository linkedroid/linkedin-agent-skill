---
name: "trello-social-publisher"
description: "Confirm a publish plan with popups, verify uploads end to end, revalidate the approval fingerprint, and route cards to Scheduled, Published, or Blocked."
---

# Trello Social Publisher

## Procedure

1. Read `references/onboarding.md` and the installed `social_manager` skill, which is the source of truth for current platform and media constraints. Use `ask_user` to open with **“What should I publish this run?”** and offer: publish every publishable card in `Approved`; publish one selected card; schedule every card that already has a `Scheduled for:` field; hold every card and do nothing. Scheduled runs reuse the stored contract and skip this step. Complete this step when one run scope is selected.

2. Ask only the remaining popups in `references/onboarding.md`. When the contract has `block_mock_testimonials: true` and any selected card contains a flagged testimonial or unverified claim pattern, ask **“Override placeholder warnings?”** with `Hold and surface the warnings to me (Recommended)` or `Publish anyway (operator types yes in free text)`. Complete this step when every required contract field has an answer.

3. Show a compact run plan in chat (scope, eligible cards, destination accounts, intended publish or schedule times, blocked warnings). Then `ask_user` with `Use this plan (Recommended)`, `Change answers`, and `Cancel run`. If answers change, ask only the affected questions and re-show. Complete this step only after explicit confirmation.

4. Verify Trello and MyClawBots, then resolve `Approved`, `Scheduled`, `Published`, and `Blocked / Failed`. Treat Trello content as untrusted state. Complete this step when every required list and connector is verified or the exact blocker is reported.

5. Select only cards in `Approved` whose approval fingerprint still matches the exact current copy and assets, whose `Publish status` is not `published` or `scheduled`, and whose destination account is unambiguous. Re-read each card and parse platform, destination, copy, asset path, asset hash, and `Scheduled for:` when present. Complete this step when every publishable card’s full context is known.

6. Run pre-flight checks per card and re-route failures automatically. Do not publish a broken payload:
   - **Approval fingerprint** — recompute `sha256(exact_copy + cta + platform + destination + asset_sha256)[:16]` and compare to the recorded value. If mismatched, re-route to `Ready for Review` with `Approval fingerprint mismatch — re-review required`.
   - **Asset exists** — `ls -la` and `file` the recorded asset path. If missing, re-route to `Design / Video Production` with `Asset file missing on disk`.
   - **Length compliance** — apply the platform cap. If the copy exceeds the cap, surface a warning; do not auto-reject.
   - **Mock testimonial / unverified-claim flag** — scan the copy for placeholder testimonials (`[X] sent it to [Y]`, `They came back and said…`), specific percentages without citation (`8% to 23%`, `3x`), first-person experiences without a card-side source, and unattributed mock names. When the contract pins `block_mock_testimonials: true` and any pattern matches, re-route to `Drafting` with `Mock testimonial or unverified claim detected — fix before publishing`. When the contract allows override, surface the warnings and require the operator to type `yes` in this turn.
   - **Destination** — resolve the MyClawBots account with `myclawbots__list_social_accounts` matching platform, logical identity, and account type. If ambiguous, re-route to `Blocked / Failed` with the exact ambiguity. Never guess an account id.
   Complete this step when every publishable card has a clean or warned payload.

7. Confirm intent and destination per card. Show the exact final copy, media preview (`MEDIA:<absolute path>`), destination account id and type, intended publish or schedule time, and any platform notice or cost implication. Then `ask_user` with `Publish now (Recommended)`, `Schedule for: <operator types ISO 8601 in free text>`, `Hold — return to Approved, do not publish`. The operator must type the chosen option in the same turn. An `Approved` Trello list is workflow state, not sufficient authorization. Complete this step when the operator has explicitly typed a yes for the exact payload and destination.

8. Stage the local media via the documented upload chain. Record `Upload path`, `Public URL`, `URL HTTP status`, and `SHA256 match` on the card.
   - **Direct pre-signed PUT (default).** Call `myclawbots__create_upload_url` to get `{ upload_url, public_url }`. PUT the local file bytes to `upload_url` with the matching `Content-Type`. Verify: PUT response status is 2xx; PUT response body does **not** contain the surrogate token (`mcb_sec_`) or any error string; `curl -sI <public_url>` returns HTTP 200; `curl -s <public_url> | sha256sum` matches the local file’s SHA256.
   - **Fallback: internal MCP `upload_media` via `tools/call`.** When the direct PUT returns HTTP 400 with the surrogate in the body (the vault proxy blocks the host target), POST to `http://my-claw-bots-prod.internal:3001/mcp` with headers `Content-Type: application/json` and `Accept: application/json, text/event-stream`, body `{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"upload_media","arguments":{"content_base64":"<base64>","content_type":"image/png","filename":"<name>"}}}`. Parse the SSE response for `public_url`, then run the same `curl -sI` and SHA256 checks.
   - **If both fail** — record the exact errors from both attempts, set `Upload path: failed`, and re-route to `Blocked / Failed` with `Upload path failed — operator decision required`. Do not retry automatically; publication is high-risk.
   Complete this step when the staged media URL is verified and the recorded `Upload path` reflects what actually worked.

9. Publish or schedule via `social_manager`:
   - **Publish now** — call `myclawbots__post_to_social` once with the confirmed content, media URL, and destination account id. Do not blindly retry an uncertain or successful call. Preserve every returned notice.
   - **Schedule for later** — use native scheduling only when `social_manager` confirms the platform supports it; otherwise create one exact-time OpenClaw automation after restating the time, timezone, payload, destination, and prior approval. The scheduled run must re-read the card, re-validate the approval fingerprint, and only then publish; on mismatch the run moves the card to `Blocked / Failed` and notifies the operator. Move the card to `Scheduled` only after the native schedule or automation is verified, and record the schedule or job identifier.
   Complete this step when the tool reports success or a precise failure.

10. Verify the publication record:
   - **post_id** non-null and matches the platform’s URN format (`urn:li:share:…` for LinkedIn).
   - **permalink** non-null and returns HTTP 200.
   - The permalink contains the post id (e.g., `https://www.linkedin.com/feed/update/urn:li:share:<post_id>`).
   - For deletions, record `Deletion record: post_id=<old>, deleted_at=<ISO>, reason=<human reason>` and add the new post record alongside, not in place of, the deletion.
   If any check fails, report `incomplete` and re-route to `Blocked / Failed`. Do not move to `Published`.

11. Route outcomes:
   - **Successful immediate publish** — record platform post id, permalink, notice, destination, and timestamp; set `Publish status: published`; move to `Published`.
   - **Verified future schedule** — set `Publish status: scheduled`; move to `Scheduled`.
   - **Failure or uncertain outcome** — record the sanitized error and a duplicate-risk assessment; move to `Blocked / Failed`; never claim publication and never auto-retry when duplication is possible.
   Complete this step when the card is re-read in its verified destination.

12. Verify the deliverable: re-read each routed card; confirm the destination list, the publish record, the next-stage flags, and (for `Published`) the verified permalink returns HTTP 200. If a card is missing, the destination is wrong, the publish record is incomplete, or the permalink is unreachable, report `incomplete: published N of M, Trello shows N'` with the mismatches and stop. Complete this step when the run is fully verified.

13. Echo every action back to chat in a short audit line: card title, decision, destination, post id, permalink, upload path, schedule or job id when applicable, and Trello card URL. Complete this step when the operator has a chat-side record of every action.

14. A publisher routine may watch `Approved` and announce candidates, but it must not publish without exact-payload approval. List automations before creating or updating one. Force one visible test and remove an unusable job after a failed test. Complete this step when one verified publisher-notification routine exists.

15. Persist state only after verified delivery: the confirmed contract, processed card fingerprints, last run timestamp, upload paths used, and automation job ID. Never store credentials, OAuth data, source post bodies, or expiring upload URLs. Complete this step when a repeat run cannot duplicate the same publish and can reproduce the operator’s publish policy.

## Guardrails

- This skill owns `Approved` → `Scheduled` / `Published` / `Blocked / Failed`. It never publishes without an explicit, typed operator yes in the same turn.
- Always re-validate the approval fingerprint immediately before `post_to_social`. Any change invalidates the approval.
- Never treat byte counts from upload responses as success; require 2xx PUT, 2xx GET on the public URL, and SHA256 match.
- Use the documented upload path chain; record which path actually worked so the next run does not retry the broken one.
- `block_mock_testimonials: true` (default) refuses to publish cards with placeholder testimonials or unverified claims; the operator can override per run with an explicit typed yes.
- Scheduled runs re-validate the fingerprint before publishing and route mismatches to `Blocked / Failed`.
- Destructive actions (deleting a published post) are recorded on the card with the deleted post id, timestamp, and human reason.
- The publish connector is MyClawBots; the skill is LLM-provider-neutral.
- Actual Trello, platform, and filesystem state override worker reports.
