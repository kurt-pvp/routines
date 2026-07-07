# Daily Briefing Routine

Run each morning. Pull from Granola, Gmail, and Slack. Present action items only.

---

## 0. Briefing Log — Continuity

- Read `briefing_log.md` before pulling fresh data. It holds a rolling 4-8 week history of action items from past briefings, each entry checklist-style (`- [ ]` open, `- [x]` resolved).
- For any open items from prior entries, spot-check against what you find in this run's Gmail/Slack/Granola pull. If there's clear evidence one was handled (reply sent, meeting held, access granted), check it off in place in the log.
- Don't surface every open item every day. Only the Monday step (Section 5) pulls from this log into the output; other days just reconcile silently.

---

## 1. Granola — Recent Meetings

- Query `list_meetings` with `time_range: custom`, `custom_start` = yesterday, `custom_end` = today
- For each meeting returned, call `get_meetings` with all IDs in a single batch call
- Extract: decisions made, open questions, explicit next steps, and any items assigned to Kurt
- Do not output a meetings list. Kurt uses his calendar for that. Only surface what feeds into action items.

---

## 2. Gmail — Recent Emails

Kurt uses unread as a mini to-do list in his inbox. Do not focus on unread threads — he already sees those. Instead, surface read threads that have gone unactioned.

Run two queries:

1. `is:read in:inbox newer_than:7d` — recent read inbox threads. Look for:
   - Threads where the last message is from someone other than Kurt, meaning he read it but never replied
   - Threads where Kurt sent the last message but a reply came in and he has not followed up
   - Scheduling threads where a time window was offered but no confirmation was sent
   - Intro threads that were acknowledged but never moved forward

2. `from:kurt@primary.vc in:sent newer_than:14d` — Kurt's recent outbound. Look for:
   - Emails Kurt sent that have received no reply, where a reply was reasonably expected
   - Surface as "no response yet" items if the thread has been quiet for 5+ days

Page size: 20 for each query.

Ignore: automated notifications (GitHub, Zoom join alerts, OOO autoreplies, quarantine alerts), vendor outreach, and any "are you interested in this product/service" solicitations.

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

On Mondays only: prepend a "Carried Over from Last Week" section listing any items still open (`- [ ]`) in `briefing_log.md` from entries dated in the previous calendar week.

---

## 6. Formatting Rules

- No em-dashes. Use a comma, colon, or period instead.
- No meetings list in the output. See note in section 1.
- Keep bullets tight. One line per item where possible.

---

## 7. Updating the Log

At the end of every run:

- Append a new dated entry to `briefing_log.md` (top-level heading `## YYYY-MM-DD (Weekday)`) listing today's action items as an unchecked checklist, grouped loosely in the same order as the brief.
- Leave the checkbox state of prior entries as reconciled in Section 0. Don't delete unresolved items, they stay open until checked off or pruned by age.
- Prune entries older than 8 weeks from the top of the file.

## Output Format

```
## Daily Brief — [Weekday, Month DD, YYYY]

### ACTION ITEMS
[grouped by urgency as above]

**Strategic Queue:** [One item from projects.md with its next action]

---
Summary: [1-2 sentences, what's most pressing today]
```
