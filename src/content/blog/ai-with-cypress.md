---
title: Using AI with Cypress — Test Generation, Debugging, and Custom Commands
description: "How to use ChatGPT, Claude, and Copilot with Cypress: generate test files, fix failing tests, write custom commands, and create fixtures with AI."
pubDate: 2025-03-02
tags: [ai, cypress, e2e, automation]
aiTwist: tutorial
---

Cypress is one of the most popular E2E frameworks. With AI, you can go from a blank file to a working test suite faster, fix failures without digging through docs, and write custom commands and fixtures without boilerplate fatigue. Here's how to do it practically.

## 1. Generating Cypress tests from a description

Describe the user flow; get a working test file. The key is being specific about the page and actions.

**Prompt:**

```
We use Cypress with JavaScript. App: [1 sentence]. Page: [e.g. checkout page].

Generate a Cypress test file (cypress/e2e/checkout.cy.js) that tests this flow:
1. Visit /checkout
2. Fill in shipping address fields (first name, last name, address, city, zip)
3. Select "Standard Shipping" from the shipping options
4. Click "Continue to Payment"
5. Fill card number, expiry, CVV (use test card 4242 4242 4242 4242)
6. Click "Place Order"
7. Assert success page is visible with text "Order confirmed"

Use cy.get() with data-testid attributes where possible, else cy.contains() or cy.get with label.
Add a beforeEach to visit the page. Use realistic but fake data as variables at the top of the file.
```

Then run `npx cypress run` and fix any selectors that don't match your actual app.

## 2. Fixing failing Cypress tests with AI

Paste the error and the test code:

```
Our Cypress test is failing with this error: [paste error message from the terminal].
Test code: [paste the failing test or the specific cy.get line].

1. What is the likely cause of this error?
2. What should I check (DevTools selector, timing, route, alias)?
3. Suggest a fix with code example.
```

**Common Cypress failures AI diagnoses quickly:**
- `cy.get(...).type(...)` fails → element not interactable or covered by another element.
- `Timed out retrying` → wrong selector, element not yet in DOM.
- `cy.intercept` not working → route pattern doesn't match the actual request URL.
- `Assertion failed` → response data doesn't match — check what the API actually returns.

## 3. Writing Cypress custom commands with AI

Custom commands reduce duplication. Describe what you need; AI writes the command.

**Prompt:**

```
We use Cypress with TypeScript. Write a custom Cypress command called "loginAs" that:
- Accepts (email: string, password: string)
- Uses cy.request() to hit POST /api/auth/login with the credentials
- Saves the token from the response to localStorage as "authToken"
- Sets a cookie "session" with the token value
- Logs the user role from the response for debugging

Add it to cypress/support/commands.ts with the TypeScript declaration.
```

Generated output goes into `cypress/support/commands.ts`. Add the reference in `cypress/support/e2e.ts` and you're done.

## 4. Generating Cypress fixtures with AI

Fixtures are JSON files for test data. AI generates realistic (but fake) ones fast.

**Prompt:**

```
Generate a Cypress fixture file for our e-commerce app (cypress/fixtures/products.json).
Include an array of 5 products, each with:
- id (UUID)
- name (realistic product name)
- price (decimal, 5–200)
- category (one of: Electronics, Clothing, Books, Home)
- inStock (boolean)
- rating (1–5, decimal)
- imageUrl (placeholder URL)

Output valid JSON only.
```

Then use in tests: `cy.fixture('products').then((products) => { ... })`.

## 5. Intercepting and mocking APIs with AI

`cy.intercept` is powerful but has a learning curve. Describe what you need:

```
We need to mock a flaky API endpoint in Cypress. Endpoint: GET /api/orders.
It should return this response body: [paste sample JSON].
With status 200 and a 500ms delay to test loading state.
Then assert the orders list is visible after the loading spinner disappears.

Write the cy.intercept() call and the assertion.
```

AI generates: `cy.intercept('GET', '/api/orders', { delay: 500, body: [...] }).as('getOrders')` and the `cy.wait('@getOrders')` pattern.

## 6. Cypress + AI: quick checklist

| Task | What to ask AI |
|------|---------------|
| New test file | Describe the flow, ask for cy.js with beforeEach and assertions |
| Fix a failure | Paste the error + test code, ask for cause and fix |
| Custom command | Describe the behavior and signature |
| Fixture | Describe the data shape and field values |
| cy.intercept | Describe the endpoint, response, and what to assert after |
| Page Object | "Convert this test file to use a Page Object class for [page]" |

For reviewing AI-generated Cypress tests, see [Reviewing AI-Generated Tests](/blog/reviewing-ai-generated-tests). For visual regression on top of Cypress, see [UI Testing with AI](/blog/ui-testing-with-ai) (Applitools + Percy). For CI integration, see [AI in CI/CD Pipelines](/blog/ai-for-ci-cd-pipelines).
