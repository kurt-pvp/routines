# Daily Briefing Routine

Run each morning. Pull from Granola, Gmail, and Slack. Present action items first, then push proposed email responses directly to Gmail Drafts.

---

## 1. Granola — Recent Meetings

- Query `list_meetings` with `time_range: custom`, `custom_start` = yesterday, `custom_end` = today
- For each meeting returned, call `get_meetings` with all IDs in a single batch call
- Extract: decisions made, open questions, explicit next steps, and any items assigned to Kurt

---

## 2. Gmail — Recent Emails

- Query: `newer_than:1d in:inbox`
- Page size: 20
- Focus on:
  - `UNREAD` + `IMPORTANT` labeled threads
  - Threads where Kurt sent the last message and a reply has come in
  - Scheduling requests or time-sensitive offers (e.g., call availability windows)
- Ignore: automated notifications (GitHub, Zoom join alerts, OOO autoreplies, quarantine alerts)

---

## 3. Slack — Active Channels and DMs

- Search DMs and group DMs first: `channel_types: im,mpim` — query `after:YYYY-MM-DD`
- If date-filtered search returns no results, fall back to keyword search: `kurt primary` across all channel types, sorted by timestamp desc
- For any channel/DM with activity in the last 24 hours, call `slack_read_channel` with `oldest` set to yesterday's Unix timestamp to get full recent context
- Extract: action items directed at Kurt, questions awaiting his response, decisions made in thread

---

## 4. Action Items

Compile a unified list across all three sources. Group by urgency:

- **Time-sensitive today** — scheduling deadlines, expiring offers, same-day asks
- **Active deals / portfolio** — follow-ups on live transactions or portco situations
- **Relationship / scheduling** — intros to acknowledge, meetings to confirm
- **Admin / deadlines** — homework, RSVPs, registrations
- **Strategic** — longer-horizon items from coaching, partner direction, self-initiated work

If no action items exist across all sources, note that briefly.

---

## 5. Monday Morning Addition

On Mondays only: prepend a "Carried Over from Last Week" section listing any action items from the previous week's briefings that remain unresolved. Source from memory or prior Granola/Gmail context.

---

## 6. Proposed Email Responses

- Select 2–3 emails that need a reply (prioritize: unread + important, time-sensitive scheduling, warm relationships)
- Draft each response: concise, professional, matching Kurt's tone (direct, warm, no filler)
- **Do not send.** Use `Gmail.create_draft` with `replyToMessageId` set to the last message in the thread, so each draft lands pre-threaded in Gmail Drafts ready to review and send

---

## Output Format

```
## Daily Brief — [Weekday, Month DD, YYYY]

### ACTION ITEMS
[grouped by urgency as above]

### PROPOSED EMAIL RESPONSES
Draft 1 — [Subject / recipient context]
> To / CC / Subject
> Body

Draft 2 — ...
Draft 3 — ...

---
Summary: [1-2 sentences: what's most pressing today]
```
