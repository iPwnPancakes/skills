# Backend Tests

Use this guide for all backend test work. After classifying the test type, read the matching section before writing or changing tests.

## Feature Tests

### Definition

Feature tests verify the actual capabilities of the system by exercising it through its public interfaces - the same way a real client or user would. If the system exposes an HTTP API, the test sends HTTP requests. If the system is a CLI, the test invokes commands. The test must not reach into internal modules or bypass the public boundary.

### Principles

- **Test the system, not the implementation.** The test should not know or care about internal architecture. It interacts only through the public interface.
- **Do NOT mock the system under test.** The system should run as close to its real configuration as possible. Internal dependencies, modules, and layers should be real.
- **External dependencies at the boundary are acceptable to fake.** If the system calls an external third-party API or service, it is acceptable to fake that dependency at the network boundary (e.g., intercepting HTTP calls, using a fake server). But internal components of the system itself must not be mocked.
- **Set up state through the public interface when possible.** If a test needs a user to exist, prefer creating that user through the system's own API/CLI rather than directly inserting into a database. When this is impractical, explicit setup through a test helper is acceptable, but document why.
- **Assert on observable outcomes.** Assert on HTTP response codes, response bodies, CLI output, or externally visible state changes. Do not assert on internal state unless it is the only way to verify a critical side effect.

### Structure

- Each test case should represent a real capability or user scenario (e.g., "a user can create a project," "the CLI returns an error when given invalid input").
- Use descriptive test names that read as capability statements.
- Organize setup, action, and assertion phases clearly within each test case.

### Pitfalls to Avoid

- Do not test internal functions directly - that is a unit test.
- Do not mock internal modules, services, or layers of the system.
- Do not couple tests to database schemas or internal data structures.
- Do not test multiple unrelated capabilities in a single test case.

## Unit Tests

### Definition

Unit tests are focused on regression prevention. They test specific, granular behaviors: edge cases, observable side effects, state changes, error handling, and individual return values. They operate at the level of individual functions, methods, or small modules.

### Principles

- **Isolate the unit under test.** Mock or stub direct collaborators when necessary to isolate the behavior being tested. The goal is to test the unit's logic, not the behavior of its dependencies.
- **Test observable behavior, not implementation details.** Assert on return values, thrown errors, calls to dependency interfaces (side effects), and state changes. Do not assert on private internals.
- **Favor testing edge cases and boundaries.** The highest value of unit tests is catching regressions on tricky logic - null values, empty inputs, off-by-one errors, type coercion issues, permission boundaries, and error paths.
- **Keep tests fast.** Unit tests should not hit the network, file system, or database unless that IS the unit being tested. If the unit depends on I/O, mock the I/O boundary.
- **One logical assertion per test case.** Each test case should verify one specific behavior. Multiple related assertions about the same behavior are fine, but do not test unrelated behaviors in a single case.

### Structure

- Name test cases to describe the specific behavior and condition (e.g., "returns null when the input array is empty," "throws an error if the user is not authorized").
- Group related test cases together by the function or method they exercise.
- Keep setup minimal - only what is needed for the specific case.

### Mocking Philosophy

- Mock **direct collaborators** of the unit (e.g., a repository, an API client, a service the unit calls).
- Do NOT mock the unit itself or its internal private methods.
- Prefer simple stubs that return canned values over complex mock configurations.
- If mocking becomes excessively complex, it may be a signal that the unit has too many responsibilities or that this should be a different type of test.

### Pitfalls to Avoid

- Do not write unit tests for trivial getters/setters unless they contain logic.
- Do not re-test behavior already covered by a feature test unless the unit test covers a specific edge case the feature test does not.
- Do not mock so aggressively that the test is only verifying the mock setup.

## Characterization Tests

### Definition

Characterization tests document the **current** behavior of the system as it exists right now. They are not asserting that the behavior is correct - they are asserting that the behavior is what it currently is. They are typically the first step to getting untested or poorly understood code under test before a refactor, migration, or significant change.

### Core Intent

- Characterize what the system does today, not what it should do.
- Preserve currently relied-on behavior, including surprising behavior.
- Maximize refactor confidence through focused, comprehensive branch coverage.

### Required Workflow

1. **Identify the SUT entry point(s)** and all observable side effects.
2. **Build a behavior matrix** from code branches, boundaries, and propagation paths. Walk through the code and map every branch, boundary comparison, null check, default fallback, and side effect.
3. **Write focused tests** with one primary behavior axis per test.
4. **Parameterize simple permutations** using whatever parameterized/data-driven test mechanism the framework provides. Keep datasets small and communicative.
5. **Keep test names behavior-documentary and literal.**
6. **Run the smallest relevant test scope**, then expand only if needed.

### Principles

- **Describe what the code does, not what it should do.** Run the code, observe the output, and write assertions that match the current behavior - even if that behavior seems wrong or surprising.
- **Do NOT fix bugs while writing characterization tests.** If you discover a bug, write the test to match the buggy behavior and leave a comment noting the suspected bug. The purpose is to create a safety net, not to change behavior.
- **Characterize quirks intentionally.** Parsing oddities, null/default behavior, boundary equality, silent no-ops - if the code does it, the test documents it.
- **These tests are meant to be temporary or transitional.** Once the refactor is complete, characterization tests should be re-evaluated. Some may become proper unit or feature tests, and some may be deleted.

### Test Design Rules

- **One `act` step per test.** Each test should invoke the SUT once.
- **Keep assertions aligned to the test name only.** Do not assert unrelated behaviors in the same test.
- **If a test name contains "and", split it** unless the two outcomes are truly inseparable.
- **Cover both positive and negative outcomes.** For every behavior, characterize what happens and what does NOT happen (e.g., "updates the record" and "does not update the record when the value is unchanged").
- **Characterize persistence and side effects explicitly.** Related model changes, logs, soft deletes, timestamps, and any other observable mutation.

### Naming Rules

- Use sentence-style test names that read like behavior documentation.
- Make names specific enough that scanning the test list explains the protected behavior.
- Include condition + outcome in the name.
- Good pattern: `set_value_with_an_older_timestamp_keeps_the_current_last_communication_time`

### Parameterization Rules

- Prefer the framework's native parameterized test mechanism over manual in-method case arrays or loops.
- Keep each dataset small and communicative - prefer 2-3 parameters per dataset.
- Split into separate tests when parameter count starts obscuring meaning.
- Use separate tests when each case needs significantly different setup.

### Focus Heuristics - When to Split Broad Tests

Split a test when any of these apply:

- Assertions validate multiple unrelated concerns.
- Setup has multiple independent dimensions (time + type + propagation + persistence).
- The same test validates several branch families.
- A failure message would not immediately point to one behavior contract.

### Coverage Checklist for Refactor Safety

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

### When to Use

- Before refactoring a module that has no tests.
- Before migrating a system to a new architecture.
- When inheriting legacy code that needs to be understood.
- When you need confidence that a change does not alter existing behavior.

### Laravel Conventions

This subsection supplements the characterization guidance above with Laravel and PHPUnit-specific conventions.

#### Test Isolation

- Use `RefreshDatabase` for database isolation between tests.
- Use model factories or local test helpers to set up state. Prefer factories when available.

#### Time Handling

- Use fixed timestamps with `Carbon::create(...)` for deterministic assertions.
- Use `Carbon::setTestNow()` when the SUT depends on "now" (e.g., `Carbon::now()`, `now()`).
- Always reset test time or rely on the framework's automatic teardown.

#### Parameterization

- Prefer `#[TestWith]` over data providers.
- Prefer `#[TestWith]` over in-method `$cases` arrays or loops.
- Keep each `#[TestWith]` dataset small and communicative.
- Prefer 2-3 parameters per dataset; split tests when parameter count starts obscuring meaning.
- Use separate tests when each case needs significantly different arrange/setup.

#### Assertions

- Assert persisted side effects with direct model queries (e.g., `$this->assertDatabaseHas(...)`, fresh model retrieval).
- Assert soft deletes, timestamps, and related model state explicitly.

#### Naming

- Use snake_case sentence-style method names per the parent characterization naming rules.
- PHPUnit will recognize public methods prefixed with `test_` or annotated with `#[Test]`. Choose whichever convention the project already uses.

#### Formatting and Running

- Run the formatter on changed files: `./vendor/bin/pint --dirty`
- Run the targeted test suite: `./vendor/bin/sail test <path> --testsuite=<Suite>`
- Run the smallest relevant scope first. Expand only if needed.

### Pitfalls to Avoid

- Do not mix characterization tests with tests that assert intended/correct behavior. Keep them clearly separated and labeled.
- Do not refactor the code under test while writing characterization tests. The code must remain unchanged.
- Do not delete characterization tests without replacing them with proper tests that cover the same behavior.
- Do not write tests with vague names like "it works" or "test basic behavior."
- Do not assert on multiple unrelated behaviors in a single test.

## Integration Tests

### Definition

Integration tests verify the interaction between the system under test and another system or service. The focus is on the boundary between the two systems - ensuring that requests are correctly formed, responses are correctly handled, and failure modes at the boundary are accounted for.

### Principles

- **The system under test runs for real.** Do not mock the internals of the system under test. It should be running its actual code, the same as it would in a feature test.
- **The external system should be faked at the network boundary.** Spin up a fake or stub version of the external service. This fake should be realistic enough to exercise the integration points, but you control it entirely. Examples: a fake HTTP server, a stub gRPC service, a test double for a message queue consumer.
- **Test the contract, not the external system's logic.** You are not testing that the external system works correctly. You are testing that your system sends the right requests, handles expected responses, and gracefully handles errors or unexpected responses from the external system.
- **Cover failure modes explicitly.** Integration boundaries are where things break: timeouts, malformed responses, authentication failures, rate limits, network errors. Test these cases.

### Structure

- Name test cases to describe the interaction (e.g., "sends a POST to the payment service with the correct payload," "retries once on a 503 from the notification service," "returns a user-friendly error when the upstream service times out").
- Clearly separate the setup of the fake external service from the setup of the system under test.
- Each test case should focus on one interaction scenario.

### Mocking Philosophy

- **Do NOT mock internals** of the system under test. The request must flow through the real code of the system.
- **DO fake the external system** at the network/protocol boundary. The fake should be configured per test to return specific responses.
- If the integration involves a database or data store that is shared between two systems, use a real instance of that data store (e.g., a test database) rather than mocking it.

### Pitfalls to Avoid

- Do not test the external system's behavior - only your system's behavior at the boundary.
- Do not mock internal HTTP clients, service layers, or repositories. Let the real code execute.
- Do not write integration tests for internal module-to-module interactions - that is a unit test or feature test.
- Do not ignore error and failure scenarios. The happy path alone is insufficient for integration tests.
