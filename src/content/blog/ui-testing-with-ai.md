---
title: UI Testing with AI — Visual Regression, Self-Healing, and Smarter Selectors
description: "How AI is used in UI and E2E testing: visual diffs, self-healing locators, and generating stable selectors from natural language."
pubDate: 2025-02-05
tags: [ai, ui-testing, e2e, visual-regression]
aiTwist: tutorial
---

UI tests break when the UI changes: buttons get new classes, text moves, and selectors become brittle. AI is showing up in **visual regression** (what changed?), **self-healing** (update the selector when the UI changes), and **test generation** (describe the flow in words, get a script). Here’s how to use it without losing control.

## 1. Visual regression with AI

Traditional pixel diffing fails on minor, harmless changes (fonts, spacing, timestamps). **AI-backed visual testing** (e.g. Applitools, Percy with AI ignore, or similar) uses ML to decide whether a diff is "semantically" the same or a real functional change. You get fewer false positives and can still catch layout breaks and missing elements.

**How to use it well:**

- **Define clear baselines** — Capture after a known-good deploy or after stabilizing the page. Re-baseline when you intentionally change the UI.
- **Scope what you compare** — Full page is noisy; prefer regions or components. Use ignore regions for dynamic content (dates, IDs) and let AI handle the rest.
- **Run in CI** — Visual checks should run on every PR or main branch so regressions are caught before release.

**Prompt idea for planning:** Ask an LLM to list "5 high-value screens or components to add visual regression tests first" given a short description of your app. Use that as a prioritization list, then implement with the tool of your choice.

## 2. Self-healing and locator maintenance

"Self-healing" tools (e.g. Testim, Mabl, Functionize) keep tests running when the UI changes by **suggesting new locators** when the old one fails. They often use a mix of DOM structure, attributes, and sometimes ML to rank the best candidate. You still approve or reject the fix.

**What to expect:**

- **Fewer total failures** — Many breakages become "suggested update" instead of "test red."
- **Review is mandatory** — Auto-accepting every suggestion can mask real bugs (e.g. the button moved and now you’re clicking the wrong one). Always review and re-run.
- **Not free** — These platforms usually have cost and lock-in. Start with a small suite (e.g. 10–20 critical flows) and measure maintenance time before scaling.

**If you’re writing code yourself (Playwright, Selenium):** Use **stable selectors** so there’s less to "heal." Prefer `getByRole`, `getByLabel`, `getByTestId`. You can ask an LLM: "Suggest Playwright locators for this HTML that are resilient to class and structure changes," then paste a snippet and refine the suggestions.

## 3. Generating UI tests from description

You can describe a flow in plain language and have an LLM generate a **draft** UI test:

- "Test: user opens login page, enters invalid email, clicks submit, sees error message."
- "Test: on the cart page, user changes quantity to 0 and sees item removed and total updated."

The model outputs Playwright, Cypress, or Selenium-style code. You then:

- **Fix selectors** — Replace brittle classes/IDs with role or label-based locators.
- **Add assertions** — Ensure you’re checking the right text, state, or URL.
- **Handle waits** — Remove arbitrary `sleep`s; use framework waiting or explicit expectations.

Use AI to get from "idea" to "first draft" quickly; you own stability and correctness. See the [Prompt library](/prompts/) for a ready-made UI test prompt.

## 4. When to prefer AI-assisted UI testing

- **Visual:** Many similar screens (e.g. product list, dashboards) where layout and content matter and you want to reduce pixel-diff noise.
- **Self-healing:** Large legacy suites with fragile selectors where you’re willing to pay for a platform to reduce maintenance.
- **Generation:** Greenfield or new flows where you want a fast first draft and will refactor for stability.

**When to be cautious:** Security-critical flows (login, payment, permissions)—keep these in code you fully control and review. Don’t rely on AI to decide "this is still the same behavior" for those without human review.

## Try this: one prompt for a UI test draft

Paste this into ChatGPT or Claude, then replace the bracketed parts and add your HTML or a short description of the screen:

```
We're writing a UI test in Playwright. Flow:

[E.g. User goes to /login, fills email and password, clicks "Sign in", and should see the dashboard with "Welcome" text.]

Assume the page has standard form fields and a submit button. Generate a single test that:
1. Goes to the page
2. Fills the fields (use getByRole or getByLabel)
3. Clicks submit
4. Asserts the expected outcome (e.g. URL change or visible text)

Use stable selectors only (no class names or brittle IDs). Add a short comment for each step.
```

Then run the test, fix any selectors or waits, and add it to your suite. For more on reviewing AI-generated tests, see [Reviewing AI-generated tests](/blog/reviewing-ai-generated-tests). For tools, see [Tools we use](/tools/) (Applitools, Testim, Mabl).
