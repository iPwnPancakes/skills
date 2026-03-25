# Backend Unit Tests

## Definition

Unit tests are focused on regression prevention. They test specific, granular behaviors: edge cases, observable side effects, state changes, error handling, and individual return values. They operate at the level of individual functions, methods, or small modules.

## Principles

- **Isolate the unit under test.** Mock or stub direct collaborators when necessary to isolate the behavior being tested. The goal is to test the unit's logic, not the behavior of its dependencies.
- **Test observable behavior, not implementation details.** Assert on return values, thrown errors, calls to dependency interfaces (side effects), and state changes. Do not assert on private internals.
- **Favor testing edge cases and boundaries.** The highest value of unit tests is catching regressions on tricky logic — null values, empty inputs, off-by-one errors, type coercion issues, permission boundaries, and error paths.
- **Keep tests fast.** Unit tests should not hit the network, file system, or database unless that IS the unit being tested. If the unit depends on I/O, mock the I/O boundary.
- **One logical assertion per test case.** Each test case should verify one specific behavior. Multiple related assertions about the same behavior are fine, but do not test unrelated behaviors in a single case.

## Structure

- Name test cases to describe the specific behavior and condition (e.g., "returns null when the input array is empty," "throws an error if the user is not authorized").
- Group related test cases together by the function or method they exercise.
- Keep setup minimal — only what is needed for the specific case.

## Mocking Philosophy

- Mock **direct collaborators** of the unit (e.g., a repository, an API client, a service the unit calls).
- Do NOT mock the unit itself or its internal private methods.
- Prefer simple stubs that return canned values over complex mock configurations.
- If mocking becomes excessively complex, it may be a signal that the unit has too many responsibilities or that this should be a different type of test.

## Pitfalls to Avoid

- Do not write unit tests for trivial getters/setters unless they contain logic.
- Do not re-test behavior already covered by a feature test unless the unit test covers a specific edge case the feature test does not.
- Do not mock so aggressively that the test is only verifying the mock setup.
