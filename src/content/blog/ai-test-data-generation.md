---
title: AI-Generated Test Data — What Works and What to Watch
description: Using LLMs to create test users, payloads, and edge-case data. Realistic vs biased, and when to keep humans in the loop.
pubDate: 2025-01-28
tags: [ai, test-data, data-generation]
aiTwist: opinion
---

Test data is tedious: you need valid emails, names, addresses, IDs, and sometimes complex nested JSON. LLMs can generate this quickly, but the output can be **too uniform** or **wrong for your domain**. Here’s how to use AI for test data without shooting yourself in the foot.

## What AI is good at

- **Large volumes** — "Generate 20 user objects with name, email, and role" in a consistent format.
- **Structured formats** — JSON, CSV, or table-shaped data with clear field names.
- **Edge-case ideas** — Empty strings, max length, unicode, negative numbers, or "user with no orders" when you ask explicitly.
- **Realistic-looking but fake** — Names, emails, and addresses that look real but aren’t (useful for demos or non-prod).

## What to watch for

**1. Bias and repetition**

Models tend to repeat patterns (e.g. same name style, same country). For diversity, ask explicitly: "Include varied countries, names from different cultures, and at least one entry with special characters in the name."

**2. Invalid for your system**

Generated emails might not match your validation (e.g. no plus-sign, or a specific domain). IDs might need to be from a real sequence or format. Always validate a sample against your app’s rules (e.g. signup form or API validation).

**3. PII and production**

Never paste production data into an LLM. Use only synthetic or anonymized descriptions ("user with 3 orders, last order 30 days ago"). Don’t generate data that could be mistaken for real users in production.

**4. Consistency across tests**

If test B depends on data created in test A (e.g. "order created by user X"), generate that data in a fixture or seed script, not ad hoc in the LLM. Use the same format your tests expect (e.g. same field names and types).

## A simple prompt for API payloads

```
We need test request bodies for our REST API. Schema:

[Paste JSON schema or a sample valid payload]

Generate 5 variants:
1. Minimal valid (only required fields)
2. Full valid (all optional fields filled)
3. Invalid: missing required field "X"
4. Invalid: wrong type for field "Y"
5. Edge case: empty string where allowed, or max length for a string field

Output as JSON array. Use clearly fake data (e.g. test@example.com, "Test User").
```

Then run one request per variant against your API and confirm you get the expected 200 vs 400 and error messages.

## When to skip AI for test data

- **Security-sensitive** — Tokens, passwords, or keys: use proper secret management and never LLM-generated "secrets."
- **Strict referential integrity** — When IDs must exist in the DB; use seeds or API calls to create real entities.
- **Legal or compliance** — Some regions restrict synthetic data that looks like real people; check your policies.

Use AI to **draft** test data and edge-case ideas; use your app and your rules to **validate** and keep data consistent and safe. For more on prompts, see the [Prompt library](/prompts/).
