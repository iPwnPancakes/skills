# Backend Feature Tests

## Definition

Feature tests verify the actual capabilities of the system by exercising it through its public interfaces — the same way a real client or user would. If the system exposes an HTTP API, the test sends HTTP requests. If the system is a CLI, the test invokes commands. The test must not reach into internal modules or bypass the public boundary.

## Principles

- **Test the system, not the implementation.** The test should not know or care about internal architecture. It interacts only through the public interface.
- **Do NOT mock the system under test.** The system should run as close to its real configuration as possible. Internal dependencies, modules, and layers should be real.
- **External dependencies at the boundary are acceptable to fake.** If the system calls an external third-party API or service, it is acceptable to fake that dependency at the network boundary (e.g., intercepting HTTP calls, using a fake server). But internal components of the system itself must not be mocked.
- **Set up state through the public interface when possible.** If a test needs a user to exist, prefer creating that user through the system's own API/CLI rather than directly inserting into a database. When this is impractical, explicit setup through a test helper is acceptable, but document why.
- **Assert on observable outcomes.** Assert on HTTP response codes, response bodies, CLI output, or externally visible state changes. Do not assert on internal state unless it is the only way to verify a critical side effect.

## Structure

- Each test case should represent a real capability or user scenario (e.g., "a user can create a project," "the CLI returns an error when given invalid input").
- Use descriptive test names that read as capability statements.
- Organize setup, action, and assertion phases clearly within each test case.

## Pitfalls to Avoid

- Do not test internal functions directly — that is a unit test.
- Do not mock internal modules, services, or layers of the system.
- Do not couple tests to database schemas or internal data structures.
- Do not test multiple unrelated capabilities in a single test case.
