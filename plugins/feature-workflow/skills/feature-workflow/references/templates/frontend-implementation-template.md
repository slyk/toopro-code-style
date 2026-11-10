# Frontend Implementation: {Feature Title}

**ADR:** ADR-{N}-{slug}

> **Optional split spec.** Use this file only when the implementation plan was split (separate builders, or a plan past roughly 300 lines). It holds the detail of this area; the plan keeps a one-line summary and a link. Do not repeat anything written in the plan. Refer to code by class/method name, no line numbers.

## Components

### {ComponentName}
- **File:** `{path}`
- **Type:** New / Modified
- **Purpose:** {description}
- **Inputs:** `{prop}:{type}` — {description}
- **Outputs:** `{event}:{type}` — {description}
- **UI elements:** {description of visual changes}

## State Management

> Adapt to your state management approach (NgRx, Redux, signals, services, etc.)

### New State
- **Store/Service:** `{name}`
- **State shape:**
  ```typescript
  {
    field:Type // description
  }
  ```

### Actions / Events
- `{actionName}` — {when triggered, what it does}

### Selectors / Computed
- `{selectorName}` — {what it derives}

### Effects / Side Effects
- `{effectName}` — {what async operation it performs}

## Services

### {ServiceName}
- **File:** `{path}`
- **New methods:**
  - `methodName(param:Type):Observable<ReturnType>` — {description}
- **Modified methods:**
  - `existingMethod()` — {what changes and why}

## Routing

> If new routes are added or existing routes change

| Path | Component | Guard | Description |
|------|-----------|-------|-------------|
| `{/path}` | `{Component}` | `{Guard}` | {description} |

## Models / Interfaces

### {ModelName}
- **File:** `{path}`
- **Properties:**
  - `field:Type` — {description}

## Module Registration

- Add `{Component}` to `{Module}` declarations / imports
- Register route in `{routing file}`
- Provide `{Service}` in `{Module}`

## Notes

- UI/UX considerations
- Responsive behavior
- Accessibility requirements
- Important implementation details
