# Frontend Tests

Use this guide for all frontend test work. After classifying the test type, read the matching section before writing or changing tests.

## Feature Tests

### Definition

Frontend feature tests verify the capabilities of a full page by rendering it and interacting with it the way a real user would. They select elements, click buttons, fill in forms, navigate, and assert on the visible outcomes. They test the page as a whole - not individual components in isolation.

### Principles

- **Render the full page.** The test should render the entire page or route-level component, including its child components, providers, and layout. Do not cherry-pick individual components out of the page.
- **Interact the way a user would.** Select elements by accessible roles, labels, text content, or test IDs as a last resort. Click, type, submit, and navigate as a user would. Do not call component methods or set state directly.
- **Do NOT mock child components.** The page's child components should render for real. The test is verifying the page's behavior as a cohesive unit.
- **Mock at the network boundary.** If the page fetches data from an API, intercept and mock the network requests. Control what data the page receives, but let all the frontend code between the network layer and the DOM execute for real.
- **Assert on what the user sees.** Assert that text appears on screen, elements are visible or hidden, navigation occurred, or feedback was displayed. Do not assert on internal component state or Redux/store internals.

### Structure

- Name test cases as user-centric capability statements (e.g., "the user can submit the registration form," "the page displays an error message when the API returns a 500").
- Organize setup clearly: mock the API responses first, then render the page, then perform actions, then assert.
- Each test case should represent one user flow or scenario.

### Pitfalls to Avoid

- Do not render a single component in isolation - that is a unit test.
- Do not mock child components or internal hooks.
- Do not assert on component state, context values, or store internals.
- Do not bypass user interaction by programmatically setting input values or triggering events on internal handlers.
- Do not couple tests to CSS class names or DOM structure unless no better selector is available.

## Unit Tests

### Definition

Frontend unit tests focus on regression prevention for individual components. They test a single component in isolation, verifying its rendering behavior, user interactions, edge cases, and observable side effects at a granular level.

### Principles

- **Render the component in isolation.** Provide only the props, context, and minimal providers necessary for the component to render. The component is the unit under test.
- **Mock direct collaborators.** If the component depends on hooks, services, or context providers that are not part of the component itself, mock them to isolate the component's behavior.
- **Test edge cases and conditional rendering.** The highest value of frontend unit tests is verifying that the component handles empty states, loading states, error states, missing props, boundary values, and conditional branches correctly.
- **Interact naturally when testing interaction behavior.** If the test verifies that clicking a button calls a callback or changes displayed text, simulate the click as a user would. Do not call event handlers directly.
- **Assert on rendered output.** Assert that the correct text, elements, or attributes are present in the rendered output. For callback/event props, assert that they were called with the expected arguments.

### Structure

- Name test cases to describe the specific behavior and condition (e.g., "renders a placeholder when the items list is empty," "calls onDelete with the item ID when the delete button is clicked").
- Group test cases by the behavior they exercise (rendering, interaction, error handling).
- Keep setup minimal - only the props and context needed for the specific case.

### Mocking Philosophy

- Mock **props that are callbacks** with test doubles so you can assert on calls.
- Mock **external hooks or services** the component calls (e.g., API hooks, navigation hooks, custom hooks that encapsulate business logic).
- Do NOT mock the component's own internal methods or state.
- If the component renders child components that are complex, it is acceptable to mock those children to keep the test focused - but prefer shallow, simple mocks over elaborate fake implementations.

### Pitfalls to Avoid

- Do not render the entire page to test one component - that is a feature test.
- Do not test styling or visual appearance unless the component has logic-driven style changes.
- Do not snapshot test as a substitute for targeted assertions. Snapshots are fragile and provide low-signal regression coverage.
- Do not assert on internal state (e.g., `useState` values). Assert on what the component renders or the callbacks it invokes.
