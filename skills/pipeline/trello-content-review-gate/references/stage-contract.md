# Review stage contract

Input: `Ready for Review`

Outputs:

- approved → `Approved`
- copy revision → `Drafting`
- creative revision → `Design / Video Production`
- rejected/unrecoverable → `Blocked / Failed`

Eligibility:

- `Draft status: complete`
- `Creative status: complete` or `not required`
- `Review status: pending`
- exact copy and all required assets available
- recorded `li-human score` present (PASS/FAIL/skipped)

Pre-flight re-routes (run before the human sees the packet):

- missing asset file → `Design / Video Production` with `Asset file missing on disk`
- missing `li-human score` → `Design / Video Production` with `Missing li-human pass`
- asset shape mismatch (e.g., carousel under single-image contract) → `Design / Video Production` with `Asset shape does not match contract`
- length cap exceeded → surface a warning in the packet; do not auto-reject
- unverified number, percentage, testimonial, or attribution → surface a warning in the packet; do not auto-reject

Approval fingerprint:

- Definition: `sha256(exact_copy + cta + platform + destination + asset_sha256)[:16]`.
- Stored on the card as `Approval fingerprint: <hash>` immediately after `Approve`.
- Re-validated on every subsequent read of an `Approved` card. A mismatch invalidates the approval and re-routes the card to `Ready for Review` with `Approval fingerprint mismatch — re-review required`.
- Any later change to copy, asset path, asset hash, or destination invalidates the approval.

Re-review handling:

- Cards returned from `Drafting` or `Design` carry `Revision: N` where `N > 1`.
- The Review Gate renders a diff between the previous and current copy and asset.
- A new decision follows the same two-step popup flow.

Audit:

- Echo every decision to chat: card title, decision, reviewer, timestamp, approval fingerprint (when applicable), destination, and Trello card URL.
- Persist: confirmed contract, processed card fingerprints, last run timestamp, approval fingerprints, automation job ID.

Do not put credentials, source-post bodies, or expiring upload URLs in the card.
