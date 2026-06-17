# Daily Briefing Routine

Run each morning. Pull from Granola, Gmail, and Slack. Present action items first, then push proposed email responses directly to Gmail Drafts.

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

## 4. Action Items

Compile a unified list across all three sources. Group by urgency:

- **Time-sensitive today** — scheduling deadlines, expiring offers, same-day asks
- **Active deals / portfolio** — follow-ups on live transactions or portco situations
- **Relationship / scheduling** — intros to acknowledge, meetings to confirm
- **Admin / deadlines** — homework, RSVPs, registrations

Do not include strategic/Quadrant 2 items here. Those live in `projects.md`.

After the action items, add a single **Strategic Queue** line: pick one item from the Active section of `projects.md` and surface it as a brief nudge with its next action. Rotate through items across sessions. Do not list all projects.

If no action items exist across all sources, note that briefly.

---

## 5. Monday Morning Addition

On Mondays only: prepend a "Carried Over from Last Week" section listing any action items from the previous week's briefings that remain unresolved. Source from memory or prior Granola/Gmail context.

---

## 6. Formatting Rules

- No em-dashes. Use a comma, colon, or period instead.
- No meetings list in the output. See note in section 1.
- Keep bullets tight. One line per item where possible.

---

## 7. Proposed Email Drafts

**Do not draft or create any email drafts.** This section is retired. Email drafting is Kurt's call.

---

## 8. Slack Delivery

After compiling the brief, post the full output to the Slack channel **#kc_daily_brief** (channel ID: `C0BASDNBTK5`) using `slack_send_message`.

---

## Output Format

```
## Daily Brief — [Weekday, Month DD, YYYY]

### ACTION ITEMS
[grouped by urgency as above]

**Strategic Queue:** [One item from projects.md with its next action]

---
Summary: [1-2 sentences, what's most pressing today]
```
