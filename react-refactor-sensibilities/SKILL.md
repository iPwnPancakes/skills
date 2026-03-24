---
name: react-refactor-sensibilities
description: Refactor React components with a strict bias toward meaningful responsibility boundaries, not JSX shuffling. Use when splitting React components, extracting helpers, or reorganizing form/UI logic so abstractions are justified and parent components get simpler.
---

# React Refactor Sensibilities

Apply these rules when refactoring React code.

## Core Principles

### Prefer responsibility boundaries over visual grouping

```tsx
// Bad
<HeaderBlock title="Swap Enclosure" />
<BodyBlock>{/* same logic still in parent */}</BodyBlock>
<FooterBlock />

// Good
<CreateNewGatewayTab
  pumpId={pump.id}
  gateways={gateways}
  standardTagNames={standardTagNames}
/>
```

### Extract a component only when it owns coherent logic + UI together

```tsx
// Bad
<UseExistingTab
  gatewayField={gatewayField}
  statusBanner={statusBanner}
  submitButton={submitButton}
/>

// Good
function ExistingGatewayTab({ pumpId, gateways, currentGateway, slotRows }: Props) {
  const form = useAppForm({ ... });
  const selectedGatewayId = useStore(form.store, s => s.values.gateway_id?.value);
  return <>...</>;
}
```

### Keep file count low; avoid scattering small wrappers across many files

```text
# Bad
swapEnclosure/
  Header.tsx
  OldSection.tsx
  NewSection.tsx
  ExistingTabLayout.tsx
  NewTabLayout.tsx

# Good
swapEnclosure/
  ExistingGatewayTab.tsx
  CreateNewGatewayTab.tsx
  MappingTable.tsx
```

### Do not extract single-use aliases/helpers that add no new behavior

```tsx
// Bad
const toTagOptions = (tags) => tags.map(tag => ({ value: tag.id, label: tag.name }));
const options = toTagOptions(selectedGateway.tags);

// Good
const options = selectedGateway.tags.map(tag => ({ value: tag.id, label: tag.name }));
```

### Make parent components simpler after extraction, not more abstract

```tsx
// Bad
<ExistingTabShell
  gatewayField={gatewayFieldNode}
  mappingTable={mappingTableNode}
  footer={submitNode}
/>

// Good
<ExistingGatewayTab
  pumpId={pump.id}
  gateways={gateways}
  currentGateway={currentGateway}
  slotRows={slotRows}
/>
```

## Component Extraction Rules

### Extract when the unit has a clear job

```tsx
// Bad
// Extracted only because JSX looked long
function NewTabLayout(props) {
  return <>{props.children}</>;
}

// Good
function CreateNewGatewayTab({ pumpId, gateways, standardTagNames }: Props) {
  const form = useAppForm({ ... });
  const serialMatch = ...;
  return <>...</>;
}
```

### Do not extract when the unit just forwards JSX via ReactNode props

```tsx
// Bad
function UseExistingTab({ gatewayField, submitButton }: Props) {
  return <>{gatewayField}{submitButton}</>;
}

// Good
function ExistingGatewayTab({ gateways, pumpId }: Props) {
  return <form.AppField name="gateway_id">...</form.AppField>;
}
```

### Do not extract wrappers whose only purpose is grouping similar JSX

```tsx
// Bad
function OldSection() {
  return <section className="tw-space-y-4">...</section>;
}

// Good
<section className="tw-space-y-4">
  <Label>Gateway</Label>
  <MappingTable rows={currentRows} />
</section>
```

### Keep small, one-off markup inline unless extraction materially improves clarity

```tsx
// Bad
// WarningBanner.tsx used once
<WarningBanner message="This is already the current gateway." />

// Good
{isSameGateway && (
  <p className="tw-text-sm tw-text-muted-foreground">This is already the current gateway.</p>
)}
```

### Keep reusable, scoped UI pieces as components

```tsx
// Bad
// Table markup copied in multiple places
<table>...</table>

// Good
<MappingTable rows={currentRows} />
```

## Helper Function Rules

### Avoid single-use helper functions that only rename a one-liner

```tsx
// Bad
const mapTags = (tags) => tags.map(tag => tag.name);
const names = mapTags(gateway.tags);

// Good
const names = gateway.tags.map(tag => tag.name);
```

### Avoid single-use renderX helpers

```tsx
// Bad
const renderTagRow = () => <tr><td>...</td></tr>;
return <tbody>{renderTagRow()}</tbody>;

// Good
return (
  <tbody>
    <tr><td>...</td></tr>
  </tbody>
);
```

### Prefer inlining one-off transforms at call sites when readable

```tsx
// Bad
const trimSerial = (value) => value.trim();
const normalized = trimSerial(serialNumber);

// Good
const normalized = serialNumber.trim();
```

### Extract helper functions only when there is real payoff

```tsx
// Bad
const getId = (x) => x.id;
const id = getId(row);

// Good
function findBestTagMatch(tags, slotRow) {
  // non-trivial slot/address compatibility logic shared across usages
}
```

## Performance And Render Hygiene

### Do not introduce extra layers of function indirection without a payoff

```tsx
// Bad
const renderBody = () => renderTable();
return <>{renderBody()}</>;

// Good
return <>{renderTable()}</>;
```

### Avoid abstractions that increase prop plumbing and rerender surface

```tsx
// Bad
<TabShell
  headerNode={...}
  fieldNode={...}
  tableNode={...}
  footerNode={...}
  renderRow={...}
/>

// Good
<ExistingGatewayTab
  pumpId={pump.id}
  gateways={gateways}
  currentGateway={currentGateway}
  slotRows={slotRows}
/>
```

### Keep state and effects close to where they are consumed

```tsx
// Bad
// Parent owns effect used only by one tab
useEffect(() => autoMapTags(...), [selectedExistingGateway]);
<ExistingGatewayTab ... />

// Good
function ExistingGatewayTab(...) {
  useEffect(() => autoMapTags(...), [selectedExistingGateway]);
  return <>...</>;
}
```

## Refactor Checklist

1. Each new component has a clear, isolated responsibility.
2. At least one meaningful logic block moved with the JSX.
3. Parent file is shorter and easier to read.
4. No single-use alias helpers were introduced.
5. Number of files is justified by cohesion, not style preference.

