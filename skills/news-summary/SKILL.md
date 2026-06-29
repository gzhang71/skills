---
name: news-summary
description: Fetch and summarize today's top news stories from across the web. Use when the user wants a morning news briefing, daily digest, summary of current events, or wants to catch up on what happened today or this week. Triggers on "news summary", "what's in the news", "morning briefing", "today's headlines", "catch me up on news", "daily digest".
disable-model-invocation: true
---

# News Summary

Generate a concise, scannable news briefing tailored to what the user cares about.

## Arguments

`$ARGUMENTS` — optional topic focus or time range (e.g., "tech news", "US politics", "past week", "AI"). If blank, cover top stories across major categories.

## Step 1: Determine scope

From `$ARGUMENTS`, identify:
- **Topics**: tech, politics, business, science, sports, world, AI — default to top 4-5 categories
- **Time range**: today / past 24h (default) vs. past week
- **Depth**: quick headlines vs. deeper summaries

## Step 2: Fetch news

Use web search to pull current stories. Search in parallel for each category:

For each relevant category, search: `"[category] news today"` or `"[topic] latest"` with today's date.

Good sources to prioritize: Reuters, AP, Bloomberg, NYT, The Verge, Ars Technica, BBC, WSJ, TechCrunch.

Focus on: factual reporting, primary sources, multiple perspectives on contested topics.

## Step 3: Format the briefing

Structure:

```
# News Briefing — [Date]

## Top Story
**[Headline]** — [2–3 sentence summary. Link to source.]

## [Category 1, e.g. Technology]
- **[Headline]** — [1–2 sentence summary.] ([Source])
- **[Headline]** — [1–2 sentence summary.] ([Source])

## [Category 2, e.g. Business & Markets]
- ...

## [Category 3, e.g. World / Politics]
- ...

## [Category 4, e.g. Science & Health]
- ...

---
*Briefing generated [date]. Sources linked above.*
```

## Guidelines

- Lead with the single most significant story of the day as "Top Story"
- 3–5 categories, 2–4 stories each — scannable in under 2 minutes
- Each bullet: headline + 1–2 sentence summary + source
- For contested political stories: present the factual situation, note major perspectives without editorializing
- If `$ARGUMENTS` specifies a focus area, expand that section (4–6 stories) and drop unrelated categories
- Flag if a story is breaking/developing with "⚡ Breaking:"
- Note market moves briefly if relevant to business section (e.g., "S&P 500 +1.2%")
