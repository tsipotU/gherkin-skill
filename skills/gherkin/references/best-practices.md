# Gherkin Best Practices

_Read this when writing or reviewing scenarios._

---

## 1. Write for the Product Manager, Not the Programmer

Scenarios should be readable by anyone who understands the business domain — not just developers. Avoid technical language like class names, HTTP verbs, database operations, or internal identifiers.

**Too technical:**
```gherkin
Given the InventoryService marks "SKU-7731" with stock_status = OUT_OF_STOCK
When the StockEventPublisher dispatches a LOW_STOCK_EVENT to the queue
Then the `inventory` table row for "SKU-7731" has available_qty = 0
```

**Business-readable:**
```gherkin
Given a product that has sold its last unit
When the stock level drops to zero
Then the product should show as "Out of stock" on the catalogue page
```

---

## 2. One Behavior Per Scenario

Each scenario should verify exactly one path through the feature. If you catch yourself writing "and also verifies…" in a scenario name, split it.

**Doing too much:**
```gherkin
Scenario: Customer adds item to basket and sees updated totals and can remove it
  Given a signed-in customer on the product page
  When they add "Bamboo Cutting Board" to their basket
  Then the basket should contain 1 item
  And the subtotal should show £24.99
  And the basket icon should show a count of 1
  And they should be able to click "Remove"
  And after removing it the basket should be empty
```

**Focused:**
```gherkin
Scenario: Basket item count updates when a product is added
  Given a signed-in customer on the product page
  When they add "Bamboo Cutting Board" to their basket
  Then the basket icon should show a count of 1

Scenario: Customer can remove an item from the basket
  Given a signed-in customer with "Bamboo Cutting Board" in their basket
  When they remove the item
  Then their basket should be empty
```

---

## 3. Keep Scenarios to 3-5 Steps

If a scenario needs more than 5 steps, it's usually testing too much or embedding setup that belongs in a Background or a named step. Long scenarios are hard to diagnose when they fail.

Aim for: 1-2 `Given`, 1 `When`, 1-2 `Then`.

If you consistently need 6+ steps, look for:
- Setup that's duplicated across scenarios → move to `Background`
- Multiple actions in a single scenario → split into separate scenarios
- Assertions that belong in a different scenario

---

## 4. Write Descriptive Scenario Names

The name should tell you what happens without reading the steps. A good test: can someone read the names in a test report and understand what broke?

**Too vague:**
```gherkin
Scenario: Invalid input
Scenario: Test case 4
Scenario: Error
```

**Descriptive:**
```gherkin
Scenario: Subscriber with a lapsed payment card is prompted to update their details
Scenario: Search results are filtered when a category is selected
Scenario: Warehouse manager cannot delete a bin location that contains stock
```

Use the pattern: _[Actor] [action/condition] [outcome]_

---

## 5. Use Background for Shared Setup

If every scenario in a file starts with the same `Given` steps, move them to a `Background` block. This reduces duplication and makes the unique parts of each scenario stand out.

```gherkin
Background:
  Given a library member with an active account
  And they have no overdue loans

Scenario: Member borrows an available book
  When they check out "The Midnight Library"
  Then the book should appear in their active loans

Scenario: Member extends a loan that is due tomorrow
  Given they have "The Midnight Library" due tomorrow
  When they request a 14-day extension
  Then the due date should be pushed out by 14 days
```

Keep `Background` short — ideally under 4 steps. If you need a long background, consider whether the feature file is covering too much.

---

## 6. Use Scenario Outline for Data Variations

When the same flow applies to multiple inputs, use `Scenario Outline` with an `Examples` table instead of duplicating scenarios. This keeps the behavior in one place and makes the variations easy to extend.

```gherkin
Scenario Outline: Discount tier is applied based on basket value
  Given a basket with a subtotal of <subtotal>
  When the customer proceeds to checkout
  Then the discount applied should be <discount>

  Examples:
    | subtotal | discount |
    | £30      | 0%       |
    | £50      | 5%       |
    | £100     | 10%      |
```

Avoid using Scenario Outline just to vary test data when the data itself doesn't represent distinct business rules — use it when the variations meaningfully test different cases.

---

## 7. Use Rules to Group Related Scenarios

When a feature has distinct business rules, use `Rule` blocks to group related scenarios. This makes the structure of the feature explicit and helps readers understand the policy being specified.

```gherkin
Feature: Item returns

  Rule: Returns are accepted within the return window

    Scenario: Customer returns an item within 30 days of purchase
      ...

    Scenario: Customer cannot return an item after 30 days have passed
      ...

  Rule: Refunds reflect the original payment method

    Scenario: Refund is credited to the original card
      ...

    Scenario: Gift purchases are refunded as store credit
      ...
```

---

## 8. Tag Thoughtfully

Tags help organize and filter test runs. Use them consistently across the project.

Common conventions:
- `@smoke` — critical path, run on every build
- `@wip` — work in progress, not yet passing
- `@slow` — long-running, run separately
- `@critical` — high-priority business scenarios
- `@regression` — full regression suite

Always follow any tagging conventions already present in the project. If none exist, establish them early and document them.

---

## 9. Then Steps Observe Outcomes, Not Internals

`Then` steps should describe what a user or system can observe — not what happened in the database, what function was called, or what the internal state is.

**Testing internals:**
```gherkin
Then the `orders` table should have a row with `status = "cancelled"`
Then the OrderService should have called `releaseReservedStock()`
Then the redis key `cart:789` should be deleted
```

**Observing outcomes:**
```gherkin
Then the order status should show as "Cancelled"
Then the cancelled items should be back in stock
Then the customer's basket should be empty
```

If you need to verify internal state, consider whether that verification belongs in a unit test rather than a Gherkin scenario.
