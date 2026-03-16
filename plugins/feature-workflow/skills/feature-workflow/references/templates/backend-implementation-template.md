# Backend Implementation: {Feature Title}

**ADR:** ADR-{N}-{slug}

## Database Changes (Proposals for DB Engineer)

> Describe the required data structure. The DB engineer will implement these changes.

### New Collections / Tables
- **`{collection_name}`** — {purpose}
  - `field_name` ({type}) — {description}
  - `field_name` ({type}) — {description}

### Modified Collections / Tables
- **`{collection_name}`**
  - Add `field_name` ({type}) — {description}

### Relations
- `{collection_a}.{field}` → `{collection_b}` — {description}

### Indexes
- `{collection}.{field}` — {reason for index}

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
