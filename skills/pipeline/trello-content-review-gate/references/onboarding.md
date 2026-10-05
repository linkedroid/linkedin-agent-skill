# Review Gate popup onboarding

Use `ask_user`. Put the recommended choice first and suffix its label with ` (Recommended)`. Ask one question at a time unless up to three answers naturally belong together. Every question needs 2–4 selectable options; free text is supplied automatically, so never add an Other option. For specific card titles or short links, tell the operator to type them in the free-text response.

## 1. Run scope

Ask: **What should I review this run?**

- **Review every card in Ready for Review (Recommended)** — every card whose draft and creative status are complete and whose `Review status: pending` matches the contract.
- **Review one selected card** — the operator types the card title or short link in free text.
- **Re-review cards that came back from Drafting or Design** — auto-surface cards whose `Revision: N > 1`.
- **Cancel this run**.

## 2. Auto re-review

Ask: **Auto-surface re-reviews?**

- **Yes — auto-surface cards whose Revision is > 1 (Recommended)**
- **No — only what I selected**

## 3. Reviewer identifier

Ask only when the operator has not set a default reviewer.

Ask: **Who is reviewing this run?** — the operator types a name or handle in free text. The reviewer identifier is recorded on every approved card.

## 4. Run plan confirmation

Show the full plan in chat: scope, eligible card titles, auto re-review policy, reviewer identifier. Then ask:

- **Use this plan (Recommended)**
- **Change answers**
- **Cancel run**

## 5. Per-card decision

### Step A — packet preview

Render the full packet in chat: destination, exact copy, CTA, `MEDIA:<asset path>`, alt text, pre-flight warnings, source-signal summary, and revision number. For re-reviews, render the diff. Then ask:

- **Review this card (Recommended)**
- **Skip this card**
- **Pause the whole review**

### Step B — decision

After the operator chooses `Review this card`, ask:

- **Approve (Recommended)** — only when all pre-flight checks pass.
- **Revise copy** — the operator types the feedback in free text.
- **Revise creative** — the operator types the feedback in free text.
- **Reject** — the operator types a short reason in free text.

Do not infer approval from silence, reactions, or card location.
