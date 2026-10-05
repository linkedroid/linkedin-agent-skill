# Drafter popup onboarding

Use `ask_user`. Put the recommended choice first and suffix its label with ` (Recommended)`. Ask one question at a time unless up to three answers naturally belong together. Every question needs 2–4 selectable options; free text is supplied automatically, so never add an Other option. For specific card titles or URNs, tell the operator to type them in the free-text response.

## 1. Run scope

Ask: **What should I draft this run?**

- **Draft every eligible card in Ideas (Recommended)** — pick up every card whose `Draft status: not started` matches the contract.
- **Draft one selected card** — the operator types the card title or short link in free text.
- **Re-draft a specific card** — operator names a card already in `Drafting`, `Design`, `Ready for Review`, or `Blocked`.
- **Cancel this run**.

## 2. Voice policy

Ask only when the eligible cards include third-party sources.

Ask: **How should voice work for these drafts?**

- **Original in my voice (Recommended)** — new theses, fresh copy, no source phrases of 5+ words.
- **Close refresh of my own material** — preserve the operator’s own experiences, structure, and figures; only strip AI slop. Available only when `Source ownership: owned`.
- **Pause and ask per card** — stop after each card and ask which voice policy to apply.

## 3. li-human pass

Ask: **Run li-human before review?**

- **Yes — require a passing li-human score (Recommended)** — drafts that fail li-human route to `Blocked / Failed` instead of `Ready for Review`. The agent invokes `li-human` from its own skill, not via `vault-curl`.
- **No — send to review as-is** — skip the humanizer; preserve the model’s copy verbatim.

## 4. Run plan confirmation

Show the full plan in chat: scope, eligible card titles, voice policy, li-human pass, expected destinations, and any auto-downgrades. Then ask:

- **Use this plan (Recommended)**
- **Change answers**
- **Cancel run**

## 5. Per-draft decision

After writing every draft locally, ask:

- **Save and route (Recommended)**
- **Revise**
- **Skip this card**
- **Pause and inspect manually**

Do not write to Trello until the operator chooses `Save and route` for each card.
