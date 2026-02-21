---
title: Writing Test Cases with ChatGPT or Claude — Prompts That Work
description: Concrete prompts for generating test scenarios, API test cases, and exploratory charters. What to include and what to avoid.
pubDate: 2025-01-18
tags: [ai, prompts, test-design]
aiTwist: prompt
---

Getting useful test cases from an LLM is mostly about **what you put in**. Vague requests ("give me test cases") produce generic lists. Specific context and a clear output format get you something you can actually use.

## What to include in the prompt

1. **The artifact** — Paste the user story, acceptance criteria, API spec, or error message. Don’t summarize; paste the real text when possible.
2. **Scope** — Say how many scenarios you want and what types (e.g. "5 scenarios: 1 happy path, 2 validation, 2 edge cases").
3. **Output format** — Ask for a table, a numbered list, or Given/When/Then so you can copy into your test management tool.
4. **Constraints** — Mention tech (e.g. "REST API", "React form") or domain rules so the model doesn’t invent wrong assumptions.

## Prompt 1: Test scenarios from a user story

Use this when you have a user story and want structured scenarios fast.

```
Given this user story:

[Paste the full user story and acceptance criteria]

Generate 5–7 test scenarios. For each scenario provide:
1. Scenario name (short, action-oriented)
2. Preconditions
3. Steps (numbered)
4. Expected result
5. Type: happy path | validation | edge case | security

Output as a markdown table with columns: Scenario | Preconditions | Steps | Expected result | Type.
```

If the first run is too generic, add: "Include at least one scenario that checks [specific rule or boundary]."

## Prompt 2: API test cases from OpenAPI/Swagger

Use this when you have an API spec and want a test plan per endpoint.

```
Here is our API specification (OpenAPI/Swagger):

[Paste the relevant paths and schemas, or a link if the model can access it]

For each endpoint, generate test cases covering:
- 200/201 success with valid payload
- 400 validation errors (missing required field, wrong type, invalid format)
- 401/403 when auth is required
- 404 for missing resource
- One negative or edge case per endpoint (e.g. empty body, huge payload)

Format: endpoint | method | scenario | expected status | sample request body (if needed).
Output as a markdown table.
```

## Prompt 3: Exploratory test charter

Use this to get ideas for a time-boxed exploratory session.

```
We're testing [feature or screen name]. Context: [1–2 sentences].

Create an exploratory test charter with:
- Mission (one sentence)
- 5 areas to focus on (specific behaviors or risks)
- 3 test data ideas (e.g. "user with no history", "max length input")
- 2 things to avoid (e.g. "don’t assume backend is correct")

Keep each item to one line. Output as a simple list.
```

## What to avoid

- **Don’t** ask for "all possible test cases" without a cap — you’ll get a wall of text.
- **Don’t** skip the output format — you’ll get prose that’s hard to import.
- **Do** run a quick sanity check: spot-check a few scenarios against the real app or API.

Use these as templates; tweak the structure to match how your team writes and stores test cases. For more prompts, see the [Prompt library](/prompts/) on this site.
