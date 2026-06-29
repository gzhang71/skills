---
name: google-calendar
description: Review your Google Calendar to see upcoming events, daily schedule, or weekly overview. Use when the user wants to know what's on their calendar, check their schedule, find free time, or prepare for upcoming meetings. Triggers on "check my calendar", "what's on my schedule", "upcoming meetings", "calendar overview", "what do I have today/this week", "google calendar".
disable-model-invocation: true
---

# Google Calendar Review

Pull up your Google Calendar and give you a clean schedule overview.

## Arguments

`$ARGUMENTS` — optional time range or focus (e.g., "today", "this week", "next 3 days", "find free time Tuesday"). Defaults to today + next 2 days.

## Step 1: Open Google Calendar

Use the browser to navigate to Google Calendar and read the current schedule.

Open: `https://calendar.google.com`

If not logged in, ask the user to log in first (you cannot handle authentication).

## Step 2: Read the schedule

Navigate to the appropriate view:
- **"today"** → Day view
- **"this week"** or default → Week view
- **"next X days"** → start from today in week view

Read the page to extract all visible events: title, time, duration, attendees (if visible), location/link.

## Step 3: Format the summary

```
# Schedule Overview — [Date Range]

## Today, [Weekday Month Day]
- [Time] — **[Event Title]** ([duration])
  - 📍 [Location or video link if present]
  - 👥 [Attendees if visible]
- [Time] — **[Event Title]** ([duration])
- *Free block: [time range]* (if there's a meaningful gap)

## Tomorrow, [Weekday Month Day]
- [Time] — **[Event Title]** ([duration])
- ...

## [Additional days if requested]
...

---
## Quick Stats
- Meetings today: X
- Busiest day this week: [day] (X events)
- Next free slot: [time]
```

## Step 4: Proactive insights (optional)

If the user asked to "prepare" or "find free time":
- **Find free time**: Identify gaps ≥ 30 min between events
- **Back-to-back meetings**: Flag if there are 3+ consecutive meetings with no break
- **Prep reminders**: For meetings in the next 24h, note if there's prep time needed

## Guidelines

- Times in the user's local timezone (as shown in Calendar)
- Use 12h time format (9:00 AM) unless user prefers 24h
- Flag recurring events with 🔄
- Flag video calls with 📹 (Zoom, Meet, Teams)
- If calendar shows all-day events, list them at the top of each day
- If access is denied or calendar is empty, say so clearly — don't fabricate events
