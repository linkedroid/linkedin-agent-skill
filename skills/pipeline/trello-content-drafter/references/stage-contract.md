# Drafting stage contract

Input: `Ideas`
Working stage: `Drafting`
Success output: `Design / Video Production` when assets are required; otherwise `Ready for Review`
Failure output: `Blocked / Failed`

Required card fields the drafter must preserve:

- pipeline slug, brief fingerprint, and idea fingerprint
- platform and destination logical name
- objective, audience, format, hook, angle, outline, CTA
- visual concept, source ownership, and source-use policy
- drafting status

A card is eligible only when it is in `Ideas`, has `Draft status: not started`, has the required pipeline metadata, and is not an instructions/template card.

The worker claims a card by moving it to `Drafting` and verifying the move before writing. Routines should use one worker per board/stage to reduce claim races.

Single-image contract handling: if the brief pins `images_per_card: 1` or `single_image_only: true`, the drafter must downgrade any `Format: carousel` to `Format: text + one image` in the creative brief, surface the downgrade in the preview, and write exactly one image concept.

`li-human` is required by default. The agent invokes `li-human` from its own skill (read `li-human`’s `SKILL.md` and run the documented command); never via `vault-curl` or any other HTTP wrapper. Failed runs route to `Blocked / Failed` with the li-human score and the reason.
