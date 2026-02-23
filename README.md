# Gherkin Skill for Claude Code

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that helps you write Gherkin BDD feature specifications (`.feature` files) interactively, following the [Cucumber](https://cucumber.io/docs/gherkin/) standard.

## What it does

- Walks you through creating `.feature` files conversationally — asks clarifying questions before proposing scenarios
- Expands beyond the happy path: surfaces edge cases, error flows, and boundary conditions
- Scans existing `.feature` files for conflicts (behavioral equivalence, not just identical wording)
- Matches your project's existing Gherkin style and conventions
- Supports internationalization via `# language: xx` headers
- Checks the project glossary (if one exists) and flags new domain terms that aren't tracked yet

## Install

```bash
claude plugin marketplace add tsipotU/gherkin-skill
claude plugin install gherkin@tsipotu-skills
```

## Usage

Just describe what you want to specify. The skill triggers on phrases like "gherkin", "feature file", "BDD scenarios", "behavior specs", or "acceptance criteria".

```
> Create gherkins for the password reset flow
> Add a scenario for expired gift cards to the checkout feature
> Write acceptance criteria for user registration
```

## How it works

1. **Understand** — asks 2-3 targeted questions to shape the scenario set
2. **Scan** — checks for existing `.feature` files and detects conflicts
3. **Write** — produces well-structured Gherkin with Rules, Backgrounds, and Scenario Outlines where appropriate
4. **Present** — shows the full file for review before writing
5. **Write/Append** — creates a new file or appends to an existing one, matching project conventions
6. **Glossary check** — flags domain terms not yet in the project glossary
7. **Summary** — reports what was added and suggests next scenarios to explore

## Changelog

### v1.1.0

- Added glossary integration: after writing `.feature` files, scans for domain terms not in the project glossary and suggests adding them

### v1.0.0

- Initial release
- Interactive scenario creation with clarifying questions
- Conflict detection against existing `.feature` files
- i18n support via `# language: xx` headers
- Full Gherkin syntax reference bundled

## License

MIT
