# Laravel Characterization Test Conventions

This file supplements [CHARACTERIZATION.md](CHARACTERIZATION.md) with Laravel and PHPUnit-specific conventions. All principles, design rules, and heuristics from the parent guide apply. This file covers only the framework-specific details.

## Test Isolation

- Use `RefreshDatabase` for database isolation between tests.
- Use model factories or local test helpers to set up state. Prefer factories when available.

## Time Handling

- Use fixed timestamps with `Carbon::create(...)` for deterministic assertions.
- Use `Carbon::setTestNow()` when the SUT depends on "now" (e.g., `Carbon::now()`, `now()`).
- Always reset test time or rely on the framework's automatic teardown.

## Parameterization

- Prefer `#[TestWith]` over data providers.
- Prefer `#[TestWith]` over in-method `$cases` arrays or loops.
- Keep each `#[TestWith]` dataset small and communicative.
- Prefer 2-3 parameters per dataset; split tests when parameter count starts obscuring meaning.
- Use separate tests when each case needs significantly different arrange/setup.

## Assertions

- Assert persisted side effects with direct model queries (e.g., `$this->assertDatabaseHas(...)`, fresh model retrieval).
- Assert soft deletes, timestamps, and related model state explicitly.

## Naming

- Use snake_case sentence-style method names per the parent guide's naming rules.
- PHPUnit will recognize public methods prefixed with `test_` or annotated with `#[Test]`. Choose whichever convention the project already uses.

## Formatting and Running

- Run the formatter on changed files: `./vendor/bin/pint --dirty`
- Run the targeted test suite: `./vendor/bin/sail test <path> --testsuite=<Suite>`
- Run the smallest relevant scope first. Expand only if needed.
