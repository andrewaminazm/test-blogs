---
title: AI for Exploratory Testing — Charters, Heuristics, and Session Notes
description: "How AI enhances exploratory testing: generating session charters, applying testing heuristics, capturing session notes, and finding edge cases humans miss."
pubDate: 2025-02-28
tags: [ai, exploratory-testing, heuristics, test-design]
aiTwist: tutorial
---

Exploratory testing is where human judgment matters most. AI doesn't replace it — but it makes you faster and more thorough. Use AI to **generate charters**, **apply heuristics**, **find edge cases**, and **document findings** while you focus on the actual testing.

## 1. AI-generated session charters

A good charter gives the session mission, focus areas, and scope. AI generates a working draft in seconds from a feature description.

**Prompt:**

```
We're doing a 90-minute exploratory testing session on [feature name]. Context:
[2–3 sentences describing the feature, user, and how it works].

Generate a session charter with:
- Mission (one sentence starting with "Explore...")
- 5 focus areas (specific behaviors, risks, or interactions to investigate)
- 3 test data ideas (interesting inputs, user types, or states)
- 2 things not to explore (out of scope or already covered)
- Risk areas to prioritize first (1–2 high-risk spots based on context)

Keep each item to 1–2 lines. Output as a structured list.
```

You then review and edit — add domain knowledge the model wouldn't know — and run the session.

## 2. Testing heuristics with AI

Heuristics are mental shortcuts for finding bugs. SFDPOT, HICCUPPS, and FEW HICCUPS are common frameworks. Use AI to apply them to your feature:

**SFDPOT prompt (Structure, Function, Data, Platform, Operations, Time):**

```
Apply the SFDPOT heuristic to this feature: [describe feature].

For each dimension:
- Structure: what to test about the UI structure and navigation
- Function: what the feature does and key behaviors to check
- Data: input types, boundaries, invalid values, edge cases
- Platform: browsers, OS, devices, or environments to consider
- Operations: user workflows, sequences, and multi-step flows
- Time: timing, timeouts, concurrent actions, date-sensitive behavior

2–3 ideas per dimension. Output as table: Dimension | Test idea.
```

**CRUD + VADER prompt (for API or data-heavy features):**

```
Apply CRUD testing to [feature/endpoint]: Create, Read, Update, Delete — what to test for each.
Then apply VADER: Volume, Authorization, Data integrity, Error handling, Recovery.

Output: dimension | specific test idea | expected outcome. Table.
```

## 3. Edge case generation with AI

Exploratory testing thrives on finding the unexpected. AI is good at generating edge cases you might not have considered:

**Prompt:**

```
Feature: [short description]. We've covered happy paths. Generate 10 edge cases to explore:
- 3 around boundary values or limits
- 2 around concurrency or race conditions
- 2 around user state or permissions (e.g. locked account, expired session)
- 2 around data integrity (empty, null, max length, unicode, special characters)
- 1 "what if the previous step failed" recovery scenario

For each: edge case name | how to trigger | what to look for (symptom of a bug). Table.
```

## 4. Session-based test management with AI

After a session, capture findings fast:

**Session debrief prompt:**

```
I ran a 90-minute exploratory session on [feature]. Notes (raw, informal):
[paste your session notes]

Help me structure these notes into a short session report:
- Coverage: what was actually explored
- Findings: bugs, anomalies, or concerns (list with severity idea)
- Questions: things to clarify with dev or product
- Recommended next session: what to explore next and why

Keep it short—under one page. Bullet lists.
```

**Bug report from session note:**

```
During exploratory testing I found this issue (rough notes): [paste].
Turn this into a clean bug report: title, steps to reproduce, expected vs actual, environment, severity suggestion.
```

## 5. AI as a "testing idea generator" during the session

You don't have to stop and prompt mid-session. But if you're stuck or want to vary your approach, quickly paste the feature context and ask:

- "What haven't I tested yet for [feature]? I've covered: [list]."
- "Give me 5 more ways a user could misuse or abuse this feature."
- "What error states could this feature reach that I haven't hit yet?"

This is the AI equivalent of a "testing buddy" suggesting ideas while you drive.

## 6. AI and the limits of exploratory testing

AI can generate ideas, but it doesn't **observe** the app, doesn't **notice** unexpected behavior, and doesn't apply **domain knowledge** or **user intuition** the way a skilled tester does.

What AI provides:
- Speed (charter in 30 seconds instead of 10 minutes)
- Breadth (heuristics you might not always apply consciously)
- Documentation (structure for notes after the session)

What only you provide:
- Noticing something "feels wrong" even when it's not broken
- Knowing which edge case is actually high-risk for this business
- Adapting in real time as you discover behavior during the session

Use AI to **prepare and document** more efficiently; spend the session time on actual observation and investigation.

For prompts, see the [Prompt library](/prompts/) (Exploratory test charter). For structured test case design with AI, see [Writing Test Cases with ChatGPT or Claude](/blog/prompts-that-work-for-test-cases).
