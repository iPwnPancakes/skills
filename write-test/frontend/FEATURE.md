# Frontend Feature Tests

## Definition

Frontend feature tests verify the capabilities of a full page by rendering it and interacting with it the way a real user would. They select elements, click buttons, fill in forms, navigate, and assert on the visible outcomes. They test the page as a whole — not individual components in isolation.

## Principles

- **Render the full page.** The test should render the entire page or route-level component, including its child components, providers, and layout. Do not cherry-pick individual components out of the page.
- **Interact the way a user would.** Select elements by accessible roles, labels, text content, or test IDs as a last resort. Click, type, submit, and navigate as a user would. Do not call component methods or set state directly.
- **Do NOT mock child components.** The page's child components should render for real. The test is verifying the page's behavior as a cohesive unit.
- **Mock at the network boundary.** If the page fetches data from an API, intercept and mock the network requests. Control what data the page receives, but let all the frontend code between the network layer and the DOM execute for real.
- **Assert on what the user sees.** Assert that text appears on screen, elements are visible or hidden, navigation occurred, or feedback was displayed. Do not assert on internal component state or Redux/store internals.

## Structure

- Name test cases as user-centric capability statements (e.g., "the user can submit the registration form," "the page displays an error message when the API returns a 500").
- Organize setup clearly: mock the API responses first, then render the page, then perform actions, then assert.
- Each test case should represent one user flow or scenario.

## Pitfalls to Avoid

- Do not render a single component in isolation — that is a unit test.
- Do not mock child components or internal hooks.
- Do not assert on component state, context values, or store internals.
- Do not bypass user interaction by programmatically setting input values or triggering events on internal handlers.
- Do not couple tests to CSS class names or DOM structure unless no better selector is available.
