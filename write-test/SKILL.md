---
name: write-test
description: Protocol for writing, adding, or modifying tests. Determines whether the test is backend or frontend, selects the appropriate test type (feature, unit, characterization, integration), and provides opinionated guidance on how to write the test and where to place it. This skill MUST be consulted any time a test is being written, added to, or modified.
---

# Skill: write-test

## Trigger

Whenever you are about to write a new test file, add a test case to an existing test file, or modify existing tests, you MUST consult this skill before writing any code. This is your test-writing protocol.

## Decision Flow

1. **Determine the domain**: Is the code under test backend or frontend?
   - Look at the file being tested. Is it a server, CLI, API route, service, data layer, or background job? → **Backend**
   - Is it a UI component, page, view, hook, or browser-side module? → **Frontend**
   - If ambiguous, ask the user.

2. **Determine the test type**: Use the heuristics below to decide which type of test to write.

3. **Read the appropriate guide**: Once you know the domain and type, read the corresponding section in the domain guide before writing any test code.

4. **Determine placement**: Look at the existing project structure for test file conventions (location, naming, directory organization). Match what already exists. If there are no existing conventions or it is unclear, ask the user.

5. **Determine tooling**: Look at the project's existing test files, dependencies, and configuration to infer the test framework and tooling in use. If unclear, ask the user.

## Heuristics

### Backend

| If the intent is to... | Test type |
|---|---|
| Test a capability of the system through its public interface (HTTP, CLI, RPC, etc.) | [Feature](BACKEND.md#feature-tests) |
| Prevent regression on edge cases, side effects, state changes, or specific return values | [Unit](BACKEND.md#unit-tests) |
| Document the current behavior of existing code before a refactor or change | [Characterization](BACKEND.md#characterization-tests) |
| Test the interaction between the system under test and another system/service | [Integration](BACKEND.md#integration-tests) |

### Frontend

| If the intent is to... | Test type |
|---|---|
| Test a full page by rendering it and interacting with it the way a user would | [Feature](FRONTEND.md#feature-tests) |
| Test an individual component in isolation for regression prevention | [Unit](FRONTEND.md#unit-tests) |

### Additional Signals

- The user says "test this endpoint / route / command" → backend feature
- The user says "make sure this edge case doesn't break" → unit (backend or frontend depending on context)
- The user says "I'm about to refactor this" or "get this under test" → characterization (backend only)
- The user says "test that these two services work together" → integration (backend only)
- The user points at a page or route-level UI component → frontend feature
- The user points at a reusable UI component → frontend unit
- The user is adding a test case to an existing test file → match the type of the existing file and read the corresponding guide

If the intent is still ambiguous after evaluating these heuristics, ask the user.
