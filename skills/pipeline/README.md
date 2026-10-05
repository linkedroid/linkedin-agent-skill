# Trello content pipeline

Six skills that plan, draft, render, review, and publish a LinkedIn post
through a Trello board, built on top of the upstream `li-*` skills.

| Worker | What it does |
|---|---|
| `linkedin-idea-watch` | Popup onboarding, original idea cards, source exhaustion reporting |
| `trello-content-drafter` | Per-card popup onboarding, originality check, li-human pass, preview/route |
| `trello-creative-producer` | Per-asset popup onboarding, render strategy (AI / deterministic / hybrid), preview/route |
| `trello-content-review-gate` | Pre-flight checks, two-step popup, approval fingerprint, chat-side decision log |
| `trello-social-publisher` | Upload path chain (direct to internal MCP), approval fingerprint revalidation, mock-testimonial guard |
| `trello-content-pipeline-coordinator` | Popup onboarding, worker reliability log, brief-pivot rebuild, lessons log |

## How they fit

```
            linkedin-idea-watch
                    v
            trello-content-drafter
                    v
            trello-creative-producer
                    v
            trello-content-review-gate
                    v
            trello-social-publisher
```

The coordinator sets up the board, schedules the workers, and reports
pipeline health.

## Pop-up onboarding

Each worker opens with a ChatGPT-style `ask_user` popup. The first question
is "what do you want to do this run?" with branch-specific follow-ups. The
operator sees a run plan, confirms it, and the worker proceeds. Scheduled
runs reuse the stored contract and skip the popups.

## Worker integrity

Every worker re-reads its destination after writing and verifies card count,
required fields, and fingerprints before persisting state. Worker reports
that do not match Trello state are treated as `incomplete` and the run
stops.

## Brief-scoped dedup

The Scout computes a `brief_fingerprint` from the topic, ICP, voice, and
source-use policy. Processed LinkedIn post URNs are scoped per fingerprint
so a topic pivot re-processes posts for the new brief without
double-processing within one brief.
