# Backend Characterization Tests

## Definition

Characterization tests document the **current** behavior of the system as it exists right now. They are not asserting that the behavior is correct — they are asserting that the behavior is what it currently is. They are typically the first step to getting untested or poorly understood code under test before a refactor, migration, or significant change.

## Principles

- **Describe what the code does, not what it should do.** Run the code, observe the output, and write assertions that match the current behavior — even if that behavior seems wrong or surprising.
- **Do NOT fix bugs while writing characterization tests.** If you discover a bug, write the test to match the buggy behavior and leave a comment noting the suspected bug. The purpose is to create a safety net, not to change behavior.
- **Cover the happy path and the most common branches first.** You are trying to build a safety net quickly. Start with the most exercised code paths, then expand to edge cases as needed.
- **These tests are meant to be temporary or transitional.** Once the refactor is complete, characterization tests should be re-evaluated. Some may become proper unit or feature tests, and some may be deleted.

## Structure

- Name test cases descriptively to document the observed behavior (e.g., "returns -1 when the item is not found," "silently ignores duplicate entries").
- Add comments in the test explaining the context: why this characterization test exists, what refactor or change it is supporting.
- Group tests by the module or function being characterized.

## When to Use

- Before refactoring a module that has no tests.
- Before migrating a system to a new architecture.
- When inheriting legacy code that needs to be understood.
- When you need confidence that a change does not alter existing behavior.

## Pitfalls to Avoid

- Do not mix characterization tests with tests that assert intended/correct behavior. Keep them clearly separated and labeled.
- Do not refactor the code under test while writing characterization tests. The code must remain unchanged.
- Do not delete characterization tests without replacing them with proper tests that cover the same behavior.
