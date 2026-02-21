---
title: More API Testing with AI — Contracts, Fuzzing, and Test Data
description: "Go beyond basic API test cases: contract testing, negative and fuzz payloads, and maintaining tests when the API changes. Prompts and tools."
pubDate: 2025-02-14
tags: [ai, api-testing, contract-testing, automation]
aiTwist: tutorial
---

You already use AI to generate API test cases from OpenAPI specs. This post goes further: **contract-style checks**, **negative and fuzz payloads**, **test data for APIs**, and **keeping tests in sync when the API changes**.

## 1. From OpenAPI to contract-style test cases

Besides "does this endpoint return 200?", you want: **required vs optional fields**, **status codes per scenario**, and **response shape**. AI can turn an OpenAPI snippet into a **test matrix**.

**Prompt idea:**

```
We have this OpenAPI path:

[Paste path + request body schema + response codes]

Generate a contract-style test matrix:
- For each documented status (200, 400, 401, 404): one test case with request that should trigger it
- One test with missing required field → expect 400
- One test with wrong type for a field → expect 400
- One test with valid minimal body → expect 200/201 and list which response fields we must assert

Output: scenario name | request (key fields) | expected status | assertions (e.g. "body.id present"). Table format.
```

Use the table to implement tests in Postman, REST Assured, or your API test framework. Assert on status and at least one or two critical response fields so you catch breaking changes.

## 2. Negative and fuzz payloads

**Negative tests** — Invalid body, wrong content-type, extra or unknown fields. Ask the model: "Given this request body schema, generate 5 negative test cases: missing required field, wrong type, empty string where number expected, null for required, and a field with value way over max length. For each: short description and example JSON."

**Fuzz-style payloads** — To stress validation and avoid crashes, ask for "10 edge-case JSON payloads for this schema: empty object, huge string, negative number where positive expected, unicode, and 5 more that might break parsing or validation." Run them against your API in a test environment and check status codes and that the server doesn’t leak stack traces or internal details.

## 3. Test data for API tests

APIs often need **valid IDs** (user, order, product) or **tokens**. Options:

- **Seed data** — Create users/orders via API or DB seed; use their IDs in tests. AI can help **design** the seed (e.g. "We need 3 users: admin, regular, and disabled; and 2 orders per user") and you implement the seed script.
- **Synthetic payloads** — For creation endpoints, use AI to generate **realistic but fake** bodies (names, emails, amounts) so you don’t rely on production-like data. See [AI test data generation](/blog/ai-test-data-generation).

**Prompt for seed design:**

```
Our API tests need these entities: [list, e.g. 1 admin user, 2 normal users, 3 orders in different states]. We create them via [API / DB]. Suggest minimal seed data (fields and example values) so we can create them in order (e.g. users first, then orders). Output as a table or JSON.
```

## 4. When the API changes

When a new version of the API adds or removes fields or endpoints:

- **Diff the spec** — If you have old vs new OpenAPI, you can paste a summary of changes into an LLM: "Our API changed: [list]. Which of these test cases might break and what should we update? [paste test case list]."
- **Update tests in bulk** — Describe your test structure (e.g. "We have one file per endpoint with tests for 200, 400, 401") and paste the changed schema. Ask: "Suggest updates to our tests: which assertions to add, which to remove, and any new test cases for new fields or codes."

Use AI to **propose** changes; run the suite and fix any remaining failures yourself.

## 5. Checklist: API testing with AI

| Area | Use AI for | You do |
|------|------------|--------|
| Test cases from spec | Matrix of scenarios, status codes, key assertions | Implement in your framework; set env (e.g. base URL) |
| Negative / fuzz | Generate payload list and example JSON | Run in test env; interpret responses |
| Test data | Seed design, synthetic request bodies | Implement seed; never use prod data in prompts |
| API changes | Suggest which tests to update, new assertions | Apply changes; run and fix failures |

For prompts you can paste today, see the [Prompt library](/prompts/) (API section). For security-focused API testing, see [Security testing with AI](/blog/security-testing-with-ai). For Jira and traceability, see [Jira and AI for testing](/blog/jira-and-ai-for-testing).
