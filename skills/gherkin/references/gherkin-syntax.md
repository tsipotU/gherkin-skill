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
Given a support ticket with description:
  """
  The checkout page fails to load when I use Safari.
  This has happened three times this week.
  """
```

**Data Tables** — structured data:
```gherkin
Given the following products are in the catalogue:
  | name              | sku      |
  | Ceramic Mug       | MUG-001  |
  | Bamboo Notebook   | NB-042   |
```

## Scenario Outline

```gherkin
Scenario Outline: Applying a discount reduces the basket total
  Given a basket total of <original>
  When a <percent>% discount voucher is applied
  Then the payable amount should be <final>

  Examples:
    | original | percent | final |
    | 100      | 10      | 90    |
    | 80       | 25      | 60    |
```

## Internationalization

Gherkin supports writing scenarios in many spoken languages. When the user asks for scenarios in a non-English language, add the `# language: xx` header as the first line of the `.feature` file. This tells the test runner which keyword translations to use.

```gherkin
# language: fr
Fonctionnalité: Réservation d'une table
  Les clients peuvent réserver une table dans le restaurant.

  Scénario: Le client réserve une table disponible
    Soit une table disponible le "14 septembre" à 19h pour 2 personnes
    Quand le client réserve pour 2 personnes à 19h
    Alors sa réservation devrait être confirmée
```

Default to English when the user doesn't specify a language.

**Common language codes:**

| Code | Language |
|------|----------|
| `en` | English (default) |
| `fr` | French |
| `de` | German |
| `es` | Spanish |
| `nl` | Dutch |
| `ja` | Japanese |
| `zh-CN` | Chinese (Simplified) |
| `pt` | Portuguese |
| `it` | Italian |
| `ru` | Russian |

For the full list of supported languages and keyword translations, see the [Cucumber i18n reference](https://cucumber.io/docs/gherkin/languages/).
