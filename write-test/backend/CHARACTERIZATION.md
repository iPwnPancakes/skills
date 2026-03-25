# Backend Characterization Tests

## Definition

Characterization tests document the **current** behavior of the system as it exists right now. They are not asserting that the behavior is correct — they are asserting that the behavior is what it currently is. They are typically the first step to getting untested or poorly understood code under test before a refactor, migration, or significant change.

## Core Intent

- Characterize what the system does today, not what it should do.
- Preserve currently relied-on behavior, including surprising behavior.
- Maximize refactor confidence through focused, comprehensive branch coverage.

## Required Workflow

1. **Identify the SUT entry point(s)** and all observable side effects.
2. **Build a behavior matrix** from code branches, boundaries, and propagation paths. Walk through the code and map every branch, boundary comparison, null check, default fallback, and side effect.
3. **Write focused tests** with one primary behavior axis per test.
4. **Parameterize simple permutations** using whatever parameterized/data-driven test mechanism the framework provides. Keep datasets small and communicative.
5. **Keep test names behavior-documentary and literal.**
6. **Run the smallest relevant test scope**, then expand only if needed.

## Principles

- **Describe what the code does, not what it should do.** Run the code, observe the output, and write assertions that match the current behavior — even if that behavior seems wrong or surprising.
- **Do NOT fix bugs while writing characterization tests.** If you discover a bug, write the test to match the buggy behavior and leave a comment noting the suspected bug. The purpose is to create a safety net, not to change behavior.
- **Characterize quirks intentionally.** Parsing oddities, null/default behavior, boundary equality, silent no-ops — if the code does it, the test documents it.
- **These tests are meant to be temporary or transitional.** Once the refactor is complete, characterization tests should be re-evaluated. Some may become proper unit or feature tests, and some may be deleted.

## Test Design Rules

- **One `act` step per test.** Each test should invoke the SUT once.
- **Keep assertions aligned to the test name only.** Do not assert unrelated behaviors in the same test.
- **If a test name contains "and", split it** unless the two outcomes are truly inseparable.
- **Cover both positive and negative outcomes.** For every behavior, characterize what happens and what does NOT happen (e.g., "updates the record" and "does not update the record when the value is unchanged").
- **Characterize persistence and side effects explicitly.** Related model changes, logs, soft deletes, timestamps, and any other observable mutation.

## Naming Rules

- Use sentence-style test names that read like behavior documentation.
- Make names specific enough that scanning the test list explains the protected behavior.
- Include condition + outcome in the name.
- Good pattern: `set_value_with_an_older_timestamp_keeps_the_current_last_communication_time`

## Parameterization Rules

- Prefer the framework's native parameterized test mechanism over manual in-method case arrays or loops.
- Keep each dataset small and communicative — prefer 2-3 parameters per dataset.
- Split into separate tests when parameter count starts obscuring meaning.
- Use separate tests when each case needs significantly different setup.

## Focus Heuristics — When to Split Broad Tests

Split a test when any of these apply:

- Assertions validate multiple unrelated concerns.
- Setup has multiple independent dimensions (time + type + propagation + persistence).
- The same test validates several branch families.
- A failure message would not immediately point to one behavior contract.

## Coverage Checklist for Refactor Safety

Before considering characterization complete, ensure coverage of:

- Mainline / happy path behavior
- Every branch in the SUT (switch/if tree)
- Boundary comparisons (`<`, `>`, equality thresholds)
- Null, missing, or invalid input handling
- Default/fallback behavior
- Time-dependent behavior and now-fallback behavior
- Cross-module or cross-model propagation and non-propagation
- Persistence side effects (created, updated, soft-deleted, logged)
- "No-op" behavior when values do not change

## When to Use

- Before refactoring a module that has no tests.
- Before migrating a system to a new architecture.
- When inheriting legacy code that needs to be understood.
- When you need confidence that a change does not alter existing behavior.

## Framework-Specific Guidance

If the project uses a specific framework with its own conventions for characterization testing, check for a framework-specific guide:

- **Laravel**: [CHARACTERIZATION_LARAVEL.md](CHARACTERIZATION_LARAVEL.md)

If no framework-specific guide exists, infer conventions from the project's existing test files and dependencies. If unclear, ask the user.

## Pitfalls to Avoid

- Do not mix characterization tests with tests that assert intended/correct behavior. Keep them clearly separated and labeled.
- Do not refactor the code under test while writing characterization tests. The code must remain unchanged.
- Do not delete characterization tests without replacing them with proper tests that cover the same behavior.
- Do not write tests with vague names like "it works" or "test basic behavior."
- Do not assert on multiple unrelated behaviors in a single test.
