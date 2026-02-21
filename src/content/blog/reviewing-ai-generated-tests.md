---
title: Reviewing AI-Generated Tests — A Checklist
description: How to quickly spot logic bugs, flaky patterns, and missing assertions in tests written by ChatGPT or Copilot.
pubDate: 2025-01-25
tags: [ai, quality, automation, review]
aiTwist: tutorial
---

AI-generated tests can be a great starting point, but they often have the same kinds of issues: wrong assumptions, brittle selectors, and missing or incorrect assertions. Here’s a short checklist you can use in code review (or when reviewing your own drafts).

## 1. Logic and assumptions

- **Does the test actually verify the right behavior?** e.g. For "invalid login shows error," does it assert on the error message or only that the URL didn’t change?
- **Are preconditions correct?** e.g. If the test assumes "user is logged out," is there a logout step or a fresh context?
- **Are test data and expected values aligned?** e.g. If you use "invalid@example.com," does the app really treat it as invalid, or did the model guess?

**Fix:** Run the test, then temporarily break the app (e.g. change the error message) and run again. The test should fail. If it still passes, the assertion isn’t checking what you think.

## 2. Selectors and stability

- **Brittle selectors** — `page.locator('.btn-primary')`, `page.locator('#submit')`, or long CSS paths are fragile. Prefer `getByRole`, `getByLabel`, `getByTestId` (if you use data-testid).
- **Arbitrary waits** — `page.waitForTimeout(3000)` is a red flag. Use Playwright’s built-in waiting or explicit waits on a condition (e.g. `expect(locator).toBeVisible()`).
- **Over-specific or under-specific** — e.g. "first button" when there are many, or a selector that matches multiple elements.

**Fix:** Replace with stable, semantic selectors. Remove fixed timeouts; rely on auto-wait or explicit expectations.

## 3. Assertions

- **Missing assertions** — The test clicks and navigates but never checks the outcome. Add at least one clear assertion per meaningful outcome.
- **Weak assertions** — e.g. Only checking that an element exists, not that it has the right text or state. Prefer `toContainText`, `toHaveValue`, or role/state checks.
- **Wrong expectation** — e.g. Expecting "Success" when the app shows "Saved." Align with real UI copy or API responses.

**Fix:** For each user-visible outcome, add an assertion. If the model gave you a vague one, replace it with a precise matcher.

## 4. Duplication and structure

- **Copy-pasted steps** — Login or setup repeated in every test. Consider `test.beforeEach` or a helper (e.g. `loginAsUser(page)`).
- **Hardcoded URLs or credentials** — Use config (e.g. `baseURL`) and env or test fixtures for credentials.

**Fix:** Extract common setup and use configuration; keep tests focused on one behavior.

## Prompt to review with AI

You can also use an LLM to do a first pass. Paste the test code and use a prompt like this (also in the [Prompt library](/prompts/)):

```
Review these automated test cases for correctness and maintainability:

[Paste test code]

List:
1. Logic bugs or false assumptions (e.g. wrong expected value, missing precondition)
2. Flaky patterns (e.g. arbitrary waits, brittle selectors)
3. Missing assertions or weak assertions
4. Suggested improvements with code snippets where helpful
```

Then apply the checklist above to the model’s suggestions and your own read-through. AI can catch obvious issues; you ensure the test actually protects the behavior you care about.
