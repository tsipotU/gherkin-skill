# Gherkin File Organization

_Read this when scanning an existing project's structure (Step 2) or when deciding where to place a new `.feature` file (Step 5)._

---

## 1. Reading the Project's Existing Structure

Before writing or placing any `.feature` file, scan for existing patterns:

**What to look for:**
- Where are existing `.feature` files? (`features/`, `test/features/`, `spec/features/`, `src/**`)
- Are they flat or organized in subdirectories by feature area?
- What naming convention do they use? (`login.feature`, `user_login.feature`, `UserLogin.feature`)
- Do files start with tags? (`@module_name`, `@smoke`)
- Are `Rule` blocks used to group scenarios, or do files stay flat?
- Do files have a description block under `Feature:`?

**How to search:**
```
Glob: **/*.feature
Grep: "Feature:" across the project
```

Once you find existing files, read 2-3 of them to understand style. **Match what the project already does** — consistency matters more than any convention here.

---

## 2. Standard Directory Layouts

There is no single correct layout. These are common patterns:

**Simple (flat):** All features in one directory.
```
features/
  search.feature
  listings.feature
  bookings.feature
  reviews.feature
```
Works well for small projects or when the feature set is limited and unlikely to grow significantly.

**Medium (grouped by area):** Subdirectories for major product areas.
```
features/
  catalogue/
    listings.feature
    search.feature
    reviews.feature
  bookings/
    reservations.feature
    cancellations.feature
    availability.feature
  account/
    profile.feature
    notifications.feature
```
Works well for mid-size projects. Makes it easy to run all specs for one area.

**Larger (mirroring application modules):** Structure follows the application's module or domain structure.
```
features/
  discovery/
    search/
    recommendations/
  marketplace/
    listings/
    pricing/
  transactions/
    bookings/
    payments/
    refunds/
```
Works well when the application has clear bounded contexts or domain modules.

---

## 3. File Naming Conventions

Name files after the feature they describe, not the test suite or tech layer.

**Conventions in the wild:**
- `reservations.feature` — lowercase, hyphenless (most common)
- `booking-cancellation.feature` — lowercase with hyphens
- `booking_cancellation.feature` — snake_case
- `BookingCancellation.feature` — PascalCase (less common)

**Rules of thumb:**
- Use the name a product person would use for the feature
- Be specific: `listing-visibility.feature` beats `catalogue.feature`
- Match whatever casing convention already exists in the project
- One feature per file — don't combine unrelated features

---

## 4. When to Create vs. When to Append

**Append to an existing file when:**
- The new scenarios belong to a feature that already has a `.feature` file
- The behavior is a variation of what's already there (e.g., adding another `Rule` block or more `Scenario Outline` rows)
- The file is not already very long (rough guide: under 15-20 scenarios)

**Create a new file when:**
- No existing file covers this feature area
- The new behavior is conceptually distinct from any existing file
- The closest existing file is already large enough that adding more would hurt readability
- The user explicitly asks for a new file

**When appending**, place new scenarios logically:
- Under the relevant `Rule` block if one exists
- After related scenarios if no `Rule` blocks are used
- At the end of the file if placement is unclear — don't rearrange existing content

---

## 5. Placing Scenarios Within a File

When a file uses `Rule` blocks, place new scenarios under the most relevant rule. If no matching rule exists, consider whether a new `Rule` block is warranted or whether the scenario belongs at the file level.

When a file has no `Rule` blocks, group related scenarios together and place new ones near their conceptual neighbors. Happy path scenarios typically come before edge cases.

Don't reorder existing scenarios unless explicitly asked — reordering disrupts version history and can introduce confusion.

---

## 6. For Brand-New Projects

When no `.feature` files exist yet, you're establishing the conventions the project will follow. Choose deliberately:

1. **Pick a root directory.** `features/` in the project root is the most common default. `test/features/` or `spec/features/` if the project has a `test/` or `spec/` convention.

2. **Start flat.** Don't over-engineer subdirectories for a project with 3 feature files. Start flat and introduce subdirectories when the flat list becomes hard to navigate (roughly: 8-10 files).

3. **Establish naming early.** Lowercase hyphen-separated (`feature-name.feature`) is a safe default. Commit to one style from the start.

4. **Establish tagging conventions early.** Decide on `@smoke`, `@critical`, `@wip` before the project has 50 scenarios — retrofitting tags is painful.

5. **Ask the user.** For a brand-new project, confirm the root directory and naming style before writing the first file. A 30-second conversation prevents inconsistency later.
