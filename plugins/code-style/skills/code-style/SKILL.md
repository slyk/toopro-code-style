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
Exception: PHP `foreach (` and TS `switch (` frequently appear **with** a space in this codebase — both forms are accepted for those two keywords; never "fix" existing ones. `if`/`for`/`while` stay compact.

### No space after colons in type annotations
```typescript
value:string                          // ✅ TypeScript / AS3
function f(x:number):boolean { }      // ✅

value: string                         // ❌ WRONG
```
Exception: NestJS **constructor DI parameters** are written with a space after the colon (`private readonly srvc: PosTerminalService`). Method params, interface fields, and return types stay compact.

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

### Object literals — space after colon is fine
Type annotations stay compact, but **object literal values** may have a space after the colon — recent TS code prefers it, AS3 inline objects stay compact:
```typescript
const cfg = {name: t.name, host: t.host, port: t.port}; // ✅ TS (preferred in recent code)
const cfg = {host:'localhost', port:3000};              // ✅ also accepted
```
```actionscript
var p:Object = {label:"Готово", value:TS_FINISHED};     // ✅ AS3 inline — compact
```

### Optional chaining and nullish coalescing — use heavily
```typescript
const price = product?.price??0;
const name = user?.profile?.name??'Unknown';
```

### Arrow functions — both spacings accepted
Recent code writes spaced arrows; older compact arrows also exist. Never reformat one into the other:
```typescript
const ids = items.map(t => t.id);   // ✅ (dominant in recent code)
const ids = items.map(v=>v.id);     // ✅ also accepted
```

### Multi-statement one-liner — braces on ONE line
When a guard needs two short statements (log + return), keep them braced on a single line:
```typescript
if(!request.vcard) {this.lgr.warn('requestCoin: no vcard'); return 'no vcard';}
```

### Section banner comments
Group related methods in services/controllers/mediators with banner comments:
```typescript
//////////////////////////////////////////////////////////
// CONNECT METHODS
```

### Comment markers
- `//TODO` — no space after `//` (project convention)
- `//!!fut` / `//!!future` / `//!!maybe` — "future improvement / open question" markers, keep them intact
- Short inline notes often have no space after `//`: `return x;//reason` — acceptable, don't fix

### Vertical alignment is deliberate
Aligned `=>` columns in PHP arrays, aligned operators/extra spaces (`public  static`, padded `if(x)    ret -= y;`), and aligned `case` returns are **intentional readability formatting** — never collapse the extra whitespace.

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
