---
name: li-dm
description: >-
  Write connection notes and DM follow-ups that get replies - the 200-character
  invite, the first message, and the two follow-ups. Use when the user says
  "write a connection request", "DM this person", "outreach message", "how do I
  follow up", or is reaching out to someone specific on LinkedIn.
---

# li-dm

The invite note is 200 characters. The first DM decides whether there is a
second one. Neither is a pitch.

## Before writing, get the specifics

Ask for, in one batched question:

1. **Who** - name, role, company.
2. **The hook** - the actual reason to reach out now. A post they wrote, a
   thing their company shipped, a mutual connection, a talk they gave. Not
   "they fit my ICP".
3. **What the user wants** - a conversation, a referral, a job, a sale. Be
   honest internally, even if the message does not lead with it.

If Linkedroid is connected, fill in what you can before asking:
`linkedin_get_profile` for who they are and what they do now, and
`context_get` for the user's offer, ideal customer and tone. The hook still has
to be real - a profile is not a reason to message someone. Their comment on a
post, a post of their own, or a recent role change is.

If there is no specific reason to message this person today, say so. A message
with no reason is what everyone else sends, and it is why their reply rate is
2%.

## The invite note (200 characters)

```
{one specific reference to them} + {one line of who you are} + {no ask}
```

The note asks for nothing. It exists to make the accept obvious. Under 200
characters including spaces - count them and show the count.

```
Saw your post on killing the discovery call - we did the same thing in March
and it worked. I run ops at a 12-person studio. Would like to follow along.
                                                                    [187/200]
```

## The first message, after they accept

Wait a day. Then:

- **Two to four sentences.** A screen of text is a delete.
- **Reference the specific thing** from the note - continuity is the whole
  reason the note was specific.
- **Give something before asking.** A number, a template, a name, an answer.
- **One ask, and make it small.** "Worth a 15-minute call?" beats "let me walk
  you through our platform".
- **No calendar link in message one.** It reads as a funnel, because it is.

## Follow-ups

Two. That is the number.

- **+4 days** - add something new. Never "just bumping this" or "following up
  on my last message". If you have nothing new, you have no follow-up.
- **+10 days** - the close-the-loop message. Say you will stop, and mean it.
  This one gets a surprising share of the total replies, because it removes
  the pressure.

Then stop. A third follow-up converts nobody and costs the relationship.

## Never

- Never blast. A sequence sent to a cold list with no specific reason to
  reach out is what gets accounts restricted, whoever sends it.
- Never fabricate a mutual connection, a shared school, or having read
  something the user has not read.
- Never write the message that opens "I hope this message finds you well".
- Never send more than 20 invites a day. Beyond that, LinkedIn throttles the
  account, and a throttled account is a dead one.

## Output

The invite note with its character count, the first message, and both
follow-ups with the day they go out. All humanized through `/li-human`.

Without automation, the user sends every one of them by hand.

**With Linkedroid connected**, the same copy can go into a campaign instead:

1. Show the user every message first, verbatim.
2. On a yes, write them into a campaign that is still a **draft**
   (`campaigns_set_message`), one node per message. A personal line that has
   to differ per person can be an AI part, `[[ai:Name::instruction]]`, but it
   needs a plain fallback message.
3. Read it back with `campaigns_get` and show it.
4. Starting the campaign is a separate yes. Never call `campaigns_start` on the
   same approval as writing the copy.

Keep it human-paced: 15-20 new people a day, weekday working hours, and
`excludeContacted` on so nobody hears from two campaigns.
