# Backend Implementation: {Feature Title}

**ADR:** ADR-{N}-{slug}

> **Optional split spec.** Use this file only when the implementation plan was split (separate builders, or a plan past roughly 300 lines). It holds the detail of this area; the plan keeps a one-line summary and a link. Do not repeat anything written in the plan. Refer to code by class/method name, no line numbers.

## Database Changes

Listed once, in the "Database Changes (Proposals)" section of `implementation-plan.md`. Do not repeat them here; describe only how the services use the new fields.

## API Endpoints

### {METHOD} {/path}
- **Service:** `{service class}`
- **Auth:** Required / Public / Role-based ({roles})
- **Parameters:**
  - `{param}` ({type}) — {description}
- **Returns:** `{return type}`
- **Logic:**
  1. Step 1
  2. Step 2

## Service Changes

### {ServiceName}
- **File:** `{path}`
- **Changes:** Description of modifications
- **New methods:**
  - `methodName(param:Type):ReturnType` — description

## Message Queue Handlers

> If the feature involves async messaging (RabbitMQ, etc.)

### {Queue/Topic Name}
- **Pattern:** `{routing key or topic pattern}`
- **Payload:** `{type description}`
- **Handler logic:**
  1. Step 1
  2. Step 2

## Module Registration

> List any new modules, providers, or controllers to register

- Register `{ServiceName}` in `{ModuleName}`
- Add `{ControllerName}` to `{ModuleName}` controllers
- Import `{ExternalModule}` in `{ModuleName}`

## Notes

- Important implementation details
- Edge cases to handle
- Performance considerations
