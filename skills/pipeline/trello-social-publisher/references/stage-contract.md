# Publishing stage contract

Input: `Approved`

Outputs:

- future verified schedule → `Scheduled`
- successful publication → `Published`
- validation, connector, or publishing failure → `Blocked / Failed`

Eligibility:

- `Review status: approved`
- `Publish status`: `not published`
- approval fingerprint matches the exact current copy and assets
- platform and destination logical identity are present
- MyClawBots destination account id resolved with `myclawbots__list_social_accounts` (never guessed)

Required publish record on the card:

- exact destination account id and account type
- platform
- approval timestamp and approval fingerprint
- upload path used (`direct` | `internal_mcp` | `failed`)
- public URL, URL HTTP status, SHA256 match
- schedule or job ID when scheduled
- platform post id, permalink, returned notice, publication time when published
- sanitized error and duplicate-risk assessment when failed
- deletion record when a previously-published post is deleted (`post_id`, `deleted_at`, `reason`)

Pre-flight re-routes (run before any publish call):

- approval fingerprint mismatch → `Ready for Review` with `Approval fingerprint mismatch — re-review required`
- missing asset file → `Design / Video Production` with `Asset file missing on disk`
- length cap exceeded → surface a warning; do not auto-reject
- mock testimonial or unverified claim under `block_mock_testimonials: true` → `Drafting` with `Mock testimonial or unverified claim detected — fix before publishing`
- ambiguous destination → `Blocked / Failed` with the exact ambiguity

Upload path chain (the only supported path; record which one actually worked):

1. **Direct pre-signed PUT (default).** Call `myclawbots__create_upload_url`. PUT raw bytes to `upload_url` with the matching `Content-Type`. Require PUT status 2xx, no surrogate token in the body, `curl -sI <public_url>` returns 200, and `curl -s <public_url> | sha256sum` matches the local file’s hash.
2. **Fallback: internal MCP `upload_media`.** When the direct PUT returns HTTP 400 with the surrogate token in the body (the vault proxy blocks the host target), POST to `http://my-claw-bots-prod.internal:3001/mcp` with headers `Content-Type: application/json` and `Accept: application/json, text/event-stream`, body `{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"upload_media","arguments":{"content_base64":"<base64>","content_type":"image/png","filename":"<name>"}}}`. Parse the SSE response for `public_url`; run the same `curl -sI` and SHA256 checks.
3. **Both failed.** Re-route to `Blocked / Failed` with the exact errors from both attempts. Do not auto-retry.

Mock testimonial / unverified-claim patterns (configurable, default blocked):

- Placeholder testimonials: `[X] sent it to [Y]`, `They came back and said…`
- Specific percentages without citation: `8% to 23%`, `3x`
- First-person experiences without a card-side source: `I built X`, `I tested Y`, `I saw Z`
- Unattributed mock names: `John D., Head of Growth`

Any change to copy, CTA, destination, schedule, or media invalidates approval and must return the card to `Ready for Review`.
