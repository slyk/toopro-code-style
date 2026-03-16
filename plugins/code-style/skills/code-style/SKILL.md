---
name: code-style
description: >
  TooPro project code style guide. ALWAYS use this skill whenever writing, editing, or reviewing
  any code in this project — TypeScript/NestJS microservices, PHP/Drupal, or ActionScript3/Flex.
  Trigger on any code generation, code completion, refactoring, or style question. This skill
  defines mandatory spacing, formatting, and syntax conventions that MUST be followed. Use it
  even if the user doesn't explicitly ask about style — it applies to all code you write here.
---

# TooPro Code Style

This project follows a **compact, horizontal-first** style that differs significantly from standard
linting defaults. Read the appropriate reference file before writing any code.

## Language → Reference File

| Language | When | Reference |
|---|---|---|
| TypeScript / NestJS | `*.ts`, NestJS services, controllers, modules | `references/typescript.md` |
| PHP / Drupal | `*.php`, `.module`, `.inc` files | `references/php.md` |
| ActionScript3 / Flex | `*.as`, `*.mxml`, PureMVC code | `references/as3.md` |

Always also read `references/general.md` for cross-language principles.

## Core Rules (apply to ALL languages)

These are the most critical rules — breaking them is the most common mistake:

### No space after control keywords
```typescript
if(condition) { }       // ✅
for(const x of xs) { }  // ✅
while(running) { }      // ✅

if (condition) { }      // ❌ WRONG
```

### No space after colons in type annotations
```typescript
value:string                          // ✅ TypeScript / AS3
function f(x:number):boolean { }      // ✅

value: string                         // ❌ WRONG
```

### Single-line conditionals — omit braces
```typescript
if(!id) return null;                    // ✅
if(!data) throw new Error('missing');   // ✅

if (!id) {             // ❌ WRONG
  return null;
}
```

### if/else with single-statement branches — no braces, no block expansion
When both `if` and `else` branches contain a single statement, **never** use braces. Put each branch on its own line without braces. Inline comments go at the end of the line:
```typescript
// ✅ CORRECT
if(t.type==OrderProxy.TT_SELL && ord.isWarrantySplitRequired()) _prepareSplitCheckout(t, ord);
else sendNotification(MVCConst.CART_CONFIRM, t, 'show box'); // No split needed

// ❌ WRONG — expanding single-statement branches into blocks
if(t.type == OrderProxy.TT_SELL && ord.isWarrantySplitRequired()) {
    _prepareSplitCheckout(t, ord);
} else {
    // No split needed
    sendNotification(MVCConst.CART_CONFIRM, t, 'show box');
}
```
This rule applies to `for`, `while`, and `else if` branches too — if the body is one statement, omit the braces.

### Single quotes for strings
```typescript
const msg = 'hello';   // ✅
const msg = "hello";   // ❌ WRONG
```

### Long lines are preferred
- No strict line length limit — 150+ chars is fine if it keeps related code together
- Prefer one long line over breaking into multiple short lines

### No space after colon in object literals
```typescript
const cfg = {host:'localhost', port:3000}; // ✅
const cfg = {host: 'localhost', port: 3000}; // ❌ WRONG
```

### Optional chaining and nullish coalescing — use heavily
```typescript
const price = product?.price??0;
const name = user?.profile?.name??'Unknown';
```

### Arrow functions — compact, no extra spaces
```typescript
const ids = items.map(v=>v.id);    // ✅
const ids = items.map(v => v.id);  // ❌ WRONG
```

## Reading Reference Files

Before writing substantial code, read the relevant reference file:

- **TypeScript**: `references/typescript.md` — NestJS patterns, async/await, decorators,
  method chaining, union types, class static members, imports
- **PHP**: `references/php.md` — Drupal 7 patterns, hooks, Form API, db queries,
  `array()` syntax, watchdog logging
- **AS3**: `references/as3.md` — PureMVC patterns, AMF requests, MXML, access modifiers,
  private member underscore prefix, for-each loops
- **General**: `references/general.md` — naming conventions, class structure order, error handling,
  mixed-language comments (Ukrainian/Russian allowed)
