# Creative stage contract

Input: `Design / Video Production`
Success output: `Ready for Review`
Failure output: `Blocked / Failed`

Eligibility:

- `Draft status: complete`
- `Creative status: queued`
- Asset type and brief exist
- Provider/model and output settings are confirmed
- Asset path under `media/content-pipeline/<slug>/<fingerprint>/` exists locally and matches the recorded SHA256

Required completion record on the card:

- provider and exact model
- prompt summary
- render strategy (`ai` | `deterministic` | `hybrid`)
- local asset path(s) and recorded SHA256 hash
- MIME/file type and dimensions or duration when known
- accessibility/alt-text suggestion
- generation attempts and timestamp
- `Creative status: complete`
- `Review status: pending`

Format compliance:

- `text` — no asset; route directly to `Ready for Review` with `Creative status: not required`.
- `text + one image` — exactly one image asset.
- `carousel` — multiple panel assets, capped at 10; auto-downgrade to a single image when the brief pins `single_image_only: true`.
- `video` — one video asset matching the contract duration and aspect ratio.

Deterministic fallback:

- When the brief indicates a text-heavy post (lead magnet, quote, listicle, comparison), prefer the deterministic HTML/CSS template renderer over AI generation.
- The default template is `templates/lead-magnet-card.html`; the default renderer is `templates/render.js` (Playwright). Operators can override per-board.
- Deterministic renders must pass the layout-integrity check (non-overlapping bounding boxes, text inside the canvas, accent color match) before saving.

Layout-integrity rule:

- After two consecutive regenerations produce the same defect, switch to the deterministic fallback instead of continuing the regeneration loop.

Do not put credentials, source-post bodies, or expiring upload URLs in the card.
