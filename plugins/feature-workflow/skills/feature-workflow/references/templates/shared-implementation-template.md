# Shared Implementation: {Feature Title}

**ADR:** ADR-{N}-{slug}

## DTOs / Interfaces

### {DtoName}
- **File:** `{path}`
- **Used by:** {list of services/components that consume this}
- **Properties:**
  - `field:Type` — {description}
  - `field:Type` — {description}

## Types / Enums

### {TypeName}
- **File:** `{path}`
- **Definition:**
  ```typescript
  export type {TypeName} = 'value1' | 'value2';
  // or
  export enum {EnumName} { VALUE1 = 'value1', VALUE2 = 'value2' }
  ```

## Utility Functions

### {functionName}
- **File:** `{path}`
- **Signature:** `{functionName}(param:Type):ReturnType`
- **Purpose:** {description}
- **Used by:** {list of consumers}

## Constants

### {CONSTANT_NAME}
- **File:** `{path}`
- **Value:** `{value}`
- **Purpose:** {description}

## Library Changes

> For Nx monorepos: which shared libs are affected

### `{lib-name}`
- **Path:** `libs/{category}/{lib-name}`
- **Changes:** {description}
- **New exports:**
  - `{ExportName}` — {description}

## Message Types / Event Contracts

> If services communicate via messages, define the shared contracts here

### {MessageType}
- **Queue/Topic:** `{name}`
- **Payload:**
  ```typescript
  {
    field:Type // description
  }
  ```

## Notes

- Backward compatibility considerations
- Breaking changes (if any) and migration path
- Which consumers need updating after these changes
