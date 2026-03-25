# Backend Integration Tests

## Definition

Integration tests verify the interaction between the system under test and another system or service. The focus is on the boundary between the two systems — ensuring that requests are correctly formed, responses are correctly handled, and failure modes at the boundary are accounted for.

## Principles

- **The system under test runs for real.** Do not mock the internals of the system under test. It should be running its actual code, the same as it would in a feature test.
- **The external system should be faked at the network boundary.** Spin up a fake or stub version of the external service. This fake should be realistic enough to exercise the integration points, but you control it entirely. Examples: a fake HTTP server, a stub gRPC service, a test double for a message queue consumer.
- **Test the contract, not the external system's logic.** You are not testing that the external system works correctly. You are testing that your system sends the right requests, handles expected responses, and gracefully handles errors or unexpected responses from the external system.
- **Cover failure modes explicitly.** Integration boundaries are where things break: timeouts, malformed responses, authentication failures, rate limits, network errors. Test these cases.

## Structure

- Name test cases to describe the interaction (e.g., "sends a POST to the payment service with the correct payload," "retries once on a 503 from the notification service," "returns a user-friendly error when the upstream service times out").
- Clearly separate the setup of the fake external service from the setup of the system under test.
- Each test case should focus on one interaction scenario.

## Mocking Philosophy

- **Do NOT mock internals** of the system under test. The request must flow through the real code of the system.
- **DO fake the external system** at the network/protocol boundary. The fake should be configured per test to return specific responses.
- If the integration involves a database or data store that is shared between two systems, use a real instance of that data store (e.g., a test database) rather than mocking it.

## Pitfalls to Avoid

- Do not test the external system's behavior — only your system's behavior at the boundary.
- Do not mock internal HTTP clients, service layers, or repositories. Let the real code execute.
- Do not write integration tests for internal module-to-module interactions — that is a unit test or feature test.
- Do not ignore error and failure scenarios. The happy path alone is insufficient for integration tests.
