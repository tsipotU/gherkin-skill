# Gherkin Anti-Patterns

_Read this when reviewing scenarios before presenting them to the user — or when refactoring existing `.feature` files._

---

## 1. Technical Language in Steps

**Problem:** Steps written using code-level concepts (class names, HTTP methods, SQL, internal APIs) couple the spec to the implementation. When the implementation changes, the scenarios break even though the behavior hasn't changed.

**Bad:**
```gherkin
Given the ReservationManager is initialised with a MockCalendarAdapter
When PUT /api/v2/bookings/42 is called with body {"date": "2024-09-14"}
Then the row in `bookings` has confirmed = true
```

**Better:**
```gherkin
Given a customer with an upcoming reservation
When they change the date to "14 September"
Then their booking should be updated
And they should see a confirmation summary
```

---

## 2. Dependent Scenarios

**Problem:** Scenarios that rely on previous scenarios having run first. This makes test order significant, prevents parallel execution, and causes cascading failures that are hard to diagnose.

**Bad:**
```gherkin
Scenario: Seller creates a listing
  Given the seller dashboard
  When they fill in the product form and submit
  Then their listing should appear in the catalogue

Scenario: Seller edits their listing
  # Depends on "Seller creates a listing" having run first
  When they update the price on the listing from the previous scenario
  Then the catalogue should show the updated price
```

**Better:**
```gherkin
Scenario: Seller edits the price of an existing listing
  Given a seller with a published listing for "Handmade Soap" priced at £8.00
  When they update the price to £9.50
  Then the catalogue should show the new price of £9.50
```

Each scenario should set up its own preconditions in `Given` steps.

---

## 3. Testing Implementation, Not Behavior

**Problem:** Scenarios that describe how the system works internally rather than what it does for the user. These become maintenance burdens when the implementation refactors.

**Bad:**
```gherkin
Scenario: Search index is rebuilt after a product update
  Given a product with title "Blue Lamp" indexed in Elasticsearch
  When the product title is changed to "Navy Lamp"
  Then a PUT request to /indexes/products/42 should be triggered
  And the index document should contain title "Navy Lamp"
```

**Better:**
```gherkin
Scenario: Updated product title appears in search results
  Given a product titled "Blue Lamp" available in the catalogue
  When the product title is updated to "Navy Lamp"
  Then searching for "Navy Lamp" should return the product
  And searching for "Blue Lamp" should return no results
```

---

## 4. Vague Step Names

**Problem:** Steps so generic they could mean anything. Makes failures hard to diagnose and scenarios hard to reuse.

**Bad:**
```gherkin
Given the system is in the right state
When the thing happens
Then it should work
```

**Better:**
```gherkin
Given a seller whose account has been suspended for non-payment
When they attempt to publish a new listing
Then they should see "Your account must be in good standing to publish listings"
```

Name steps to describe a specific, observable state or action. Avoid "the thing", "it", "the state", "works correctly."

---

## 5. Too Many Steps Per Scenario

**Problem:** Scenarios with 8, 10, or more steps are doing too much. They're hard to read, hard to maintain, and when they fail, it's hard to tell which step revealed the problem.

**Bad:**
```gherkin
Scenario: Customer completes onboarding
  Given the onboarding wizard
  And the customer enters their business name
  And they upload a logo
  And they click Next
  And they choose their industry
  And they click Next
  And they enter their billing address
  And they click Next
  And they add a payment method
  And they click Finish
  Then they should see the main dashboard
  And the setup checklist should show 100% complete
  And they should receive a welcome email
```

**Better:** Split into focused scenarios, each covering one stage of the onboarding flow. Use `Background` for shared setup (e.g., a customer who has started the wizard). Use `Scenario Outline` if the same validation applies across multiple fields.

---

## 6. Mixing Setup and Action in Given/When

**Problem:** `Given` steps should establish preconditions — they shouldn't perform actions that are themselves under test. When `Given` does too much, the scenario obscures what's actually being tested.

**Bad:**
```gherkin
Scenario: Customer sees booking history after making a reservation
  Given the customer navigates to the booking page and reserves a table for "14 September"
  When they open their booking history
  Then they should see "14 September" in their upcoming reservations
```

**Better:**
```gherkin
Scenario: Customer sees booking history after making a reservation
  Given a customer who has a confirmed reservation for "14 September"
  When they open their booking history
  Then they should see "14 September" in their upcoming reservations
```

`Given` sets up state. `When` describes the single action being tested.

---

## 7. Overloaded Feature Files

**Problem:** A single `.feature` file that covers unrelated behaviors, or a feature with dozens of scenarios across many different business rules. These files become hard to navigate and often indicate the feature is really several features.

**Signs of an overloaded file:**
- The `Feature` description is very general ("Product management", "Bookings")
- The file has 20+ scenarios
- Scenarios in the file have nothing in common with each other
- Multiple `Rule` blocks that could stand alone as separate features

**Better:** Split into separate `.feature` files by business area. `catalogue/listing-creation.feature`, `catalogue/listing-visibility.feature`, and `catalogue/pricing.feature` is better than `catalogue.feature`.

---

## 8. Assertion-Heavy Given Steps

**Problem:** `Given` steps that include assertions (verifying something is true before the test runs) rather than just setting up state. Assertions belong in `Then`.

**Bad:**
```gherkin
Given the event is published and ticket sales are open and the venue is confirmed
And there are available seats and the attendee has a valid payment method
When they purchase a ticket
Then the ticket should be issued
```

**Better:**
```gherkin
Given a published event with available tickets
When a customer with a valid payment method purchases a ticket
Then the ticket should be issued
```

Keep `Given` focused: describe the state of the world at the start of the scenario, not assertions about it.
