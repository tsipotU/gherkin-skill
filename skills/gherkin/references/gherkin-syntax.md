# Gherkin Syntax Reference

## File Structure

Each `.feature` file contains exactly one `Feature`. Features contain scenarios grouped optionally by `Rule`.

```gherkin
# language: en
Feature: Short feature title
  A longer description of the feature.
  Can span multiple lines.

  Background:
    Given shared precondition for all scenarios

  Rule: Business rule grouping (optional, Gherkin 6+)

    Example: Concrete scenario name
      Given some precondition
      When an action happens
      Then an expected outcome

    Example: Another scenario
      Given some precondition
      When a different action happens
      Then a different outcome
```

## Keywords

| Keyword | Purpose |
|---------|---------|
| `Feature` | Top-level grouping, one per file. Followed by colon and title. |
| `Rule` | Groups scenarios under a business rule (optional). |
| `Scenario` / `Example` | A concrete test case. Synonyms. |
| `Given` | Establish initial context (preconditions). |
| `When` | Describe the action or event. |
| `Then` | Assert the expected outcome. |
| `And` / `But` | Continue the previous step type for readability. |
| `*` | Wildcard step keyword (for list-like steps). |
| `Background` | Shared `Given` steps run before each scenario. |
| `Scenario Outline` | Parameterized scenario with `<placeholders>`. |
| `Examples` | Data table for Scenario Outlines. |
| `@tag` | Tags for organizing and filtering. |
| `#` | Comments (full line only). |

## Step Arguments

**Doc Strings** — multi-line text:
```gherkin
Given a blog post with body:
  """
  This is the blog post body.
  It spans multiple lines.
  """
```

**Data Tables** — structured data:
```gherkin
Given the following users exist:
  | name   | email              |
  | Alice  | alice@example.com  |
  | Bob    | bob@example.com    |
```

## Scenario Outline

```gherkin
Scenario Outline: Eating changes cucumber count
  Given there are <start> cucumbers
  When I eat <eat> cucumbers
  Then I should have <left> cucumbers

  Examples:
    | start | eat | left |
    | 12    | 5   | 7    |
    | 20    | 5   | 15   |
```

## Best Practices

- 3-5 steps per scenario maximum
- Write in business language, not technical implementation
- `Then` steps should observe user-visible outcomes, not database internals
- Keep `Background` short (under 4 lines)
- Use vivid, descriptive scenario names
- One behavior per scenario — if you need "and also", split it
- Tags help organize: `@smoke`, `@wip`, `@critical`, `@slow`
