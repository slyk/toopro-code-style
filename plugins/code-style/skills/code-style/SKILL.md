---
name: code-style
description: >
  TooPro code style for TypeScript/NestJS, PHP/Drupal 7 and ActionScript3/Flex: a compact,
  horizontal-first style that differs from Prettier and common linter defaults. Use it before
  writing, editing or reviewing code in one of these three languages in a TooPro project.
---

# TooPro Code Style

This project follows a **compact, horizontal-first** style that differs significantly from standard
linting defaults. The core rules below apply to all three languages and are written **only here**;
the reference files add language specifics and do not repeat them.

## First: change only your own lines

The style applies to the lines you write. Do not reformat lines you are not changing, do not run
Prettier or any other formatter or format-on-save over source files, and do not "fix" style in
surrounding code while making a change. Formatters rewrite this style into the standard one, and a
reformatted file hides the real change in the diff.

## Language → Reference File

| Language | When | Reference |
|---|---|---|
| TypeScript / NestJS | `*.ts`, NestJS services, controllers, modules | `references/typescript.md` |
| PHP / Drupal | `*.php`, `.module`, `.inc` files | `references/php.md` |
| ActionScript3 / Flex | `*.as`, `*.mxml`, PureMVC code | `references/as3.md` |

Read the reference file of the language you are writing. Read `references/general.md` when you write
comments or docs, name something new, or lay out a new class or file.

The rules come in three tiers: what new code always does, where two forms are both fine, and what is
never touched.

## Tier 1 — Always, in code you write

These are consistent across the codebase and are what makes the style recognisable.

### No space after `if` / `for` / `while`
```typescript
if(condition) { }       // ✅
for(const x of xs) { }  // ✅
while(running) { }      // ✅

if (condition) { }      // ❌ WRONG
```

### Opening brace on the same line (K&R)
```typescript
class MyClass {
  method() {
    if(condition) {
    }
  }
}
```
Never put `{` on its own line.

### No space after colons in type annotations
Method params, return types, interface fields, class fields and local variables:
```typescript
value:string                          // ✅ TypeScript / AS3
function f(x:number):boolean { }      // ✅

value: string                         // ❌ WRONG
```

### Single-statement branches — no braces
```typescript
if(!id) return null;                    // ✅
if(!data) throw new Error('missing');   // ✅

if (!id) {             // ❌ WRONG
  return null;
}
```
Validate first and return early: guards go at the top of the function as one-line conditionals.

When both `if` and `else` hold a single statement, neither gets braces. Each branch goes on its own line, and an inline comment goes at the end of the line, never inside a block. The same holds for `else if`, `for` and `while` bodies of one statement.
```typescript
// ✅ CORRECT
if(result.success) return {payment, status: 'completed', transactionId: result.id};
else return {payment, status: 'failed', error: result.error}; // gateway refused
```
```actionscript
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

### Two-statement guard — braces on ONE line
When a guard needs two short statements (log + return), keep them braced on a single line:
```typescript
if(!request.vcard) {this.lgr.warn('requestCoin: no vcard'); return 'no vcard';}
```

### Single quotes for strings
```typescript
const msg = 'hello';   // ✅
const msg = "hello";   // ❌ WRONG
```
PHP uses double quotes for interpolation; AS3 has many existing double-quoted strings — see those reference files.

### Long lines
- No line length limit — 150+ chars is fine if it keeps related code together
- Prefer one long line over breaking into several short ones

### Optional chaining and compact `??`
Use `?.` and `??` heavily, with no spaces around `??`:
```typescript
const price = product?.price??0;
const name = user?.profile?.name??'Unknown';
```

### Section banners and `//TODO`
Group related methods in services/controllers/mediators with banner comments:
```typescript
//////////////////////////////////////////////////////////
// CONNECT METHODS
```
Write `//TODO` with no space after `//`.

## Tier 2 — Either form is fine

Both forms exist in the codebase. In a file you are editing, write what its neighbouring lines use; in a new file, use the form marked "recent". Never convert one form into the other.

| What | Recent / more common | Also accepted |
|---|---|---|
| Comparison operators | `if(status === 'active')` | `if(status==='active')` |
| Arrow functions | `items.map(t => t.id)` | `items.map(v=>v.id)` |
| TS object literals | `{name: t.name, host: t.host}` | `{host:'localhost', port:3000}` |
| AS3 inline objects | `{label:"Готово", value:TS_FINISHED}` | spaced, in multiline objects |
| NestJS constructor DI params | `private readonly srvc: PosTerminalService` | `private readonly srvc:PosTerminalService` |
| `switch` | `switch (value)` | `switch(value)` |
| PHP `foreach` | `foreach ($items as $item)` | `foreach($items as $item)` |
| Short inline note | `return x; // reason` | `return x;//reason` |

Constructor DI params are the only type annotation where a space after the colon is normal; everywhere else Tier 1 applies.

## Tier 3 — Never touch

- **Existing lines** you are not changing (see "First" above).
- **Vertical alignment.** Extra spaces or tabs that line related code into columns are deliberate: aligned `=>` in PHP arrays, padded operators, `public  static`, aligned `case` returns. Never collapse them.
  ```php
  'help'		=> 'Check status...',
  'file'		=> array('type'=>'inc', 'module'=>'tps_pay_method'),
  'callback'	=> '_tpsrr_rro_works',
  ```
  ```actionscript
  if(this._bonusEnabled) ret -= vw.bonusComponent.getFinalValueToUse();
  if(this._allowDebt)    ret -= vw.numDebt.value;
  ```
- **Project markers** `//!!fut`, `//!!future`, `//!!maybe` ("future improvement / open question"): keep them exactly as written.
- **File-top design notes, commented-out code with a reason, closing-bracket markers, native-language comments** — described in `references/general.md`; they are documentation, not clutter.

## Reference Files

Each file holds only what is not already stated above:

- **TypeScript**: `references/typescript.md` — generics and union types, type inference, method chaining,
  ternaries, static members, imports, NestJS controller patterns, a complete example
- **PHP**: `references/php.md` — Drupal 7 patterns, hooks, Form API, db queries, `array()` syntax,
  `=>` spacing, quotes, watchdog logging
- **AS3**: `references/as3.md` — PureMVC patterns, AMF requests, MXML, access modifiers,
  private member underscore prefix, quotes, for-each loops
- **General**: `references/general.md` — doc blocks, mixed-language comments (Ukrainian/Russian allowed),
  file-top notes, commented-out code, naming, class structure order
