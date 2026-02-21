---
title: Using GitHub Copilot to Write Playwright Tests — A 15-Minute Win
description: Get from zero to a few working Playwright tests with Copilot. What to type, what to accept, and what to fix.
pubDate: 2025-01-22
tags: [ai, copilot, playwright, automation]
aiTwist: tutorial
---

You don’t need to learn every Playwright API before writing tests. If you have GitHub Copilot (or Cursor) in your editor, you can go from a blank file to a small suite in about 15 minutes by **describing what you want in plain English** and letting the model suggest the code.

## Setup

- Install [Playwright](https://playwright.dev/) in your project (`npm init playwright@latest` or add it to an existing Node repo).
- Ensure Copilot is enabled for the file (e.g. `.spec.ts` or `.test.ts`).

## What to type to get good suggestions

**1. Test file comment at the top**

```ts
// Tests for the login page: valid login, invalid credentials, and empty submit.
```

Then start typing `test(` or `test('valid login'` — Copilot will often suggest a full test.

**2. One test at a time with a clear name**

```ts
test('user can log in with valid email and password', async ({ page }) => {
  // Copilot suggests: goto, fill, click, expect
});
```

**3. When you need a selector**

Type a comment and let Copilot fill in the call:

```ts
// Go to login page and fill email and password, then click Submit
```

You’ll often get something like `page.goto('/login')`, `page.getByRole('textbox', { name: 'Email' }).fill(...)`, etc. Prefer **role-based** or **label-based** selectors; if Copilot gives you a brittle CSS selector, replace it with `getByRole` or `getByLabel`.

**4. Assertions**

```ts
// Check that the dashboard is visible and shows "Welcome"
```

Copilot will suggest `expect(page.getByText('Welcome')).toBeVisible()` or similar. Add or adjust as needed.

## What to fix before you commit

- **URLs** — Copilot might use `/login` or `https://example.com/login`. Point to your real app (e.g. baseURL in config or env).
- **Selectors** — Replace `page.locator('.btn-primary')` with something stable like `page.getByRole('button', { name: 'Submit' })`.
- **Waits** — Prefer Playwright’s auto-waiting; remove unnecessary `page.waitForTimeout(2000)` if Copilot added it.
- **Data** — Use real (or test) credentials; don’t commit production secrets.

## Example: three tests in one file

Start with this and let Copilot complete each test body:

```ts
import { test, expect } from '@playwright/test';

test.describe('Login page', () => {
  test('valid credentials redirect to dashboard', async ({ page }) => {
    // TODO: goto login, fill valid email/password, click submit, expect dashboard
  });

  test('invalid credentials show error message', async ({ page }) => {
    // TODO: goto login, fill invalid data, click submit, expect error alert
  });

  test('empty submit shows validation errors', async ({ page }) => {
    // TODO: goto login, click submit without filling, expect validation messages
  });
});
```

Replace each `// TODO` with a short comment describing the steps; accept and then refine Copilot’s suggestion. Run with `npx playwright test` and fix any failures (usually selectors or URLs).

## Bottom line

Use Copilot to **draft** tests quickly; use your judgment to **stabilize** selectors, URLs, and assertions. For more on reviewing AI-generated tests, see [Reviewing AI-generated tests](/blog/reviewing-ai-generated-tests) and the [Prompt library](/prompts/) for review prompts.
