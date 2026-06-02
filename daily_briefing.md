# Daily Briefing Routine

Run each morning. Pull from Granola, Gmail, and Slack. Present action items first, then push proposed email responses directly to Gmail Drafts. Deliver the final brief to Kurt via Slack DM.

---

## 1. Granola — Recent Meetings

- Query `list_meetings` with `time_range: custom`, `custom_start` = yesterday, `custom_end` = today
- For each meeting returned, call `get_meetings` with all IDs in a single batch call
- Extract: decisions made, open questions, explicit next steps, and any items assigned to Kurt
- Do not output a meetings list. Kurt uses his calendar for that. Only surface what feeds into action items.

---

## 2. Gmail — Recent Emails

- Query: `newer_than:1d in:inbox`
- Page size: 20
- Focus on:
  - `UNREAD` + `IMPORTANT` labeled threads
  - Threads where Kurt sent the last message and a reply has come in
  - Scheduling requests or time-sensitive offers (e.g., call availability windows)
- Ignore: automated notifications (GitHub, Zoom join alerts, OOO autoreplies, quarantine alerts), vendor outreach, and any "are you interested in this product/service" solicitations — do not surface these in the brief or draft responses to them

---

## 3. Slack — Active Channels and DMs

- Search DMs and group DMs first: `channel_types: im,mpim` — query `after:YYYY-MM-DD`
- If date-filtered search returns no results, fall back to keyword search: `kurt primary` across all channel types, sorted by timestamp desc
- For any channel/DM with activity in the last 24 hours, call `slack_read_channel` with `oldest` set to yesterday's Unix timestamp to get full recent context
- Extract: action items directed at Kurt, questions awaiting his response, decisions made in thread

---

## 4. Google Calendar — Verify What's Already Scheduled

Before compiling action items, query Google Calendar for events created or modified in the last 24 hours:

- Call `list_events` on Kurt's primary calendar with `timeMin` = yesterday, `timeMax` = tomorrow+7 days
- Use this to cross-check scheduling action items: if a meeting is already on the calendar, do not surface "schedule a call with X" as an action item
- Also note any new meetings appearing tomorrow or later this week that were not on the calendar yesterday — these may reflect items already actioned

---

## 5. Completion Check — Do Not Surface Already-Done Items

Before finalizing the action item list, run a verification pass. This step is mandatory — do not skip it.

**Sent mail check:** Run a single broad Gmail query: `in:sent newer_than:2d`. Scan the results for any emails to people or on topics that match candidate action items. Search by name or keyword — do not rely solely on exact email addresses, as those may not match. A sent email to that person on that subject is sufficient to drop the item.

**Calendar check:** If the action item is a scheduling ask (set up a call, send a calendar invite), verify no event already exists with that person in Section 4 results before surfacing it.

**Slack confirmation check:** In the same Slack threads where the action item originated, look for a reply from Kurt indicating completion: "sent," "done," "scheduled," "on it," "just shared," etc.

**Rule:** If any of the three checks show clear evidence of completion, drop the item entirely — do not include it, even with a "done" note. Only live, unresolved items belong in the brief. When genuinely uncertain, include the item with a "Verify:" flag.

---

## 6. Action Items

Compile a unified list across all sources. Group by urgency:

- **Time-sensitive today** — scheduling deadlines, expiring offers, same-day asks
- **Active deals / portfolio** — follow-ups on live transactions or portco situations
- **Relationship / scheduling** — intros to acknowledge, meetings to confirm
- **Admin / deadlines** — homework, RSVPs, registrations

Do not include strategic/Quadrant 2 items here. Those live in `projects.md`.

After the action items, add a single **Strategic Queue** line: pick one item from the Active section of `projects.md` and surface it as a brief nudge with its next action. Rotate through items across sessions. Do not list all projects.

If no action items exist across all sources, note that briefly.

---

## 7. Monday Morning Addition

On Mondays only: prepend a "Carried Over from Last Week" section listing any action items from the previous week's briefings that remain unresolved. Source from memory or prior Granola/Gmail context.

---

## 8. Formatting Rules

- No em-dashes. Use a comma, colon, or period instead.
- No meetings list in the output. See note in section 1.
- Keep bullets tight. One line per item where possible.

---

## 9. Proposed Email Drafts

- **Do not draft replies to unread emails.** Reading and responding to those is Kurt's call.
- Instead, identify 2–3 situations where a **net-new email** would move something forward — e.g., intro emails Kurt needs to make, outreach to someone mentioned in a meeting, or a follow-up to a warm external contact.
- **External recipients only.** Primary is Slack-first — never propose emailing a @primary.vc colleague. Internal to-dos and follow-ups stay in Slack.
- Draft each email: concise, professional, matching Kurt's tone (direct, warm, no filler)
- Always include Kurt's signature in the email body:

```
--
Kurt Chessman
VP, Strategic Finance & Corporate Development
Primary | Twitter | LinkedIn | Newsletter
We always want to meet top founders
303.842.2029
```

- **Do not send.** Use `Gmail.create_draft` so each draft lands in Gmail Drafts ready to review and send. For replies, set `replyToMessageId`. For net-new emails, omit it.

---

## 10. Slack Delivery

After compiling the brief and pushing Gmail drafts, post the complete brief to Kurt's Slack DM using `slack_send_message` with `channel_id: U08G4GWKN22`. Tag him at the top with `<@U08G4GWKN22>` so it triggers a notification. Use the same output format as below.

---

## Output Format

```
## Daily Brief — [Weekday, Month DD, YYYY]

### ACTION ITEMS
[grouped by urgency as above]

**Strategic Queue:** [One item from projects.md with its next action]

### PROPOSED EMAILS
Draft 1 — [Subject / recipient context]
> To / CC / Subject
> Body

Draft 2 — ...
Draft 3 — ...

---
Summary: [1-2 sentences, what's most pressing today]
```
