# TypeScript Code Style Guide

This document defines the specific TypeScript code style used in this project. These rules prioritize compactness and horizontal space usage over vertical expansion.

## Braces and Control Flow

### Opening Braces
**Always** place opening brace on the same line (K&R style):
```typescript
// ✅ Correct
class MyClass {
  method() {
    if (condition) {
      // code
    }
  }
}

// ❌ Wrong
class MyClass
{
  method()
  {
  }
}
```

### Single-Line Conditionals
**Omit braces** for single-line if/else statements:
```typescript
// ✅ Correct
if(condition) doSomething();
if(!value) throw new Error('missing value');
if(x>0) return x;

// ✅ Multiple related single-line conditions
if(!id) return null;
if(!data) throw new Error('no data');
const result = process(data);

// ❌ Wrong - unnecessary braces for single statement
if(condition) {
  doSomething();
}
```

### if/else with single-statement branches — never use braces
When both branches contain a single statement, write without braces. Each branch on its own line. Inline comments go at end of the line — never inside a block:
```typescript
// ✅ Correct
if(t.type==OrderProxy.TT_SELL && ord.isWarrantySplitRequired()) _prepareSplitCheckout(t, ord);
else sendNotification(MVCConst.CART_CONFIRM, t, 'show box'); // No split needed

if(result.success) return {payment, status:'completed', transactionId:result.id};
else return {payment, status:'failed', error:result.error};

// ❌ Wrong - expanding single-statement if/else into blocks
if(t.type == OrderProxy.TT_SELL && ord.isWarrantySplitRequired()) {
    _prepareSplitCheckout(t, ord);
} else {
    // No split needed - show confirm box normally
    sendNotification(MVCConst.CART_CONFIRM, t, 'show box');
}
```
Same rule applies to `else if`, `for`, and `while` — if the body is one statement, no braces.

### Multiple Statements on Same Line
Use semicolons to separate related statements on the same line:
```typescript
// ✅ Correct
if (value <= 0) value = 0; else value = Math.round(value * 100) / 100;
if (typeof result === 'string') return result; else await wait();

// ✅ Correct - sequential short statements
const id = getId(item); if (!id) return false;
```

## Spacing

### No Space After Control Keywords
**Do not** add space after `if`, `for`, `while`, `switch`:
```typescript
// ✅ Correct
if(condition) { }
for(const item of items) { }
while(running) { }
switch(value) { }

// ❌ Wrong
if (condition) { }
for (const item of items) { }
```

### No Space After Colons in Type Annotations
**Do not** add space after colon in type annotations:
```typescript
// ✅ Correct
function process(value:string, count:number):boolean {
  const result:MyType = compute();
  return true;
}

// ❌ Wrong
function process(value: string, count: number): boolean {
  const result: MyType = compute();
}
```

### No Space Around Equality in Conditionals
**Minimize** spacing around comparison operators:
```typescript
// ✅ Correct
if(value==null) return;
if(status==='active') process();
if(x===y) return true;

// ❌ Wrong
if (value == null) return;
if (status === 'active') process();
```

## Line Length and Breaking

### Prefer Long Lines
**Favor** keeping code on single lines even beyond 120-150 characters when it improves readability:
```typescript
// ✅ Correct - long line is acceptable
this.logger.log(`calc cashback for '${trans.id}' paid ${trans.value}/${trans.cost??trans.value} uah, place ${trans.place}, vcard ${trans.vcard}.`, level, logArray);

// ✅ Correct - complex return type on one line
async function getData(id:string):Promise<Pick<DbSchema['records'][number], 'id'|'status'|'created'|'updated'>> {
```

### Method Chaining
Chain methods on **single line** when reasonable, or **minimal lines**:
```typescript
// ✅ Correct - short chain on one line
const query = dbqb<Product>().fields(['id','name']).sort(['-created']).limit(10);

// ✅ Correct - longer chain with minimal lines
const query = dbqb<Product>()
  .equal('status', 'active')
  .equal('type', ProductType.RETAIL)
  .fields(['id','name','price']).limit(100).sort('-created');
```

## Strings and Quotes

### Single Quotes Only
**Always** use single quotes for strings:
```typescript
// ✅ Correct
const message = 'hello world';
const path = 'api/users';
throw new Error('invalid input');

// ❌ Wrong
const message = "hello world";
const path = "api/users";
```

### Template Literals
Use backticks for string interpolation:
```typescript
// ✅ Correct
console.log(`Processing item ${id} with status ${status}`);
const url = `${baseUrl}/api/${endpoint}`;
```

## Type Annotations

### No Spacing in Type Annotations
**Omit** spaces after colons and in generic parameters:
```typescript
// ✅ Correct
function process<T>(items:T[], mapper:(item:T)=>string):string[] {
  const result:Map<string,T> = new Map();
  const value:string|number|null = getValue();
}

// ❌ Wrong
function process<T>(items: T[], mapper: (item: T) => string): string[] {
  const result: Map<string, T> = new Map();
}
```

### Union Types Inline
**Keep** union types on single line without breaks:
```typescript
// ✅ Correct
function getValue(input:string|number|null):Promise<Result|undefined|null> {
  const data:IProduct|IService|string = fetch();
}

// ❌ Wrong - avoid breaking union types
function getValue(
  input: string | number | null
): Promise<Result | undefined | null> {
}
```

## Variables and Functions

### Type Inference Preferred
**Allow** TypeScript to infer types when obvious:
```typescript
// ✅ Correct
const count = 0;
const items = new Map<string,Product>();
const result = await fetchData();

// ❌ Unnecessary - type is obvious
const count:number = 0;
const result:Promise<Data> = await fetchData();
```

### Arrow Functions
**Inline** simple arrow functions without spacing around arrow:
```typescript
// ✅ Correct
const ids = items.map(v=>v.id);
const active = users.filter(u=>u.status==='active');
const transform = (x:number)=>x*2;

// ❌ Wrong - unnecessary spaces
const ids = items.map(v => v.id);
const transform = (x: number) => x * 2;
```

## Objects and Arrays

### Object Literals
**No space** after colons in object literals:
```typescript
// ✅ Correct
const config = {
  host:'localhost',
  port:3000,
  timeout:5000,
  enabled:true
};

// ❌ Wrong
const config = {
  host: 'localhost',
  port: 3000
};
```

### Inline Short Objects
**Keep** short objects on single line:
```typescript
// ✅ Correct
const point = {x:10, y:20};
const user = {id:'123', name:'John', active:true};

// ❌ Wrong - unnecessary line breaks for short objects
const point = {
  x: 10,
  y: 20
};
```

## Optional Chaining and Nullish Coalescing

### Heavy Use of Optional Chaining
**Prefer** optional chaining with nullish coalescing:
```typescript
// ✅ Correct
const price = product?.price??0;
const value = item.data?.value??defaultValue;
const result = response?.data?.items?.[0]?.id??null;

// ✅ Correct - complex chaining
const cost = lineItem?.price / (lineItem?.amount??1);
```

## Error Handling

### Inline Error Returns
**Use** single-line returns/throws without braces:
```typescript
// ✅ Correct
if(!id) return null;
if(!data) throw new Error('missing data');
if(!user) return 'user not found';
if(value<0) return {error:'invalid value'};

// ❌ Wrong
if (!id) {
  return null;
}
```

### Early Returns
**Favor** early returns without braces:
```typescript
// ✅ Correct
function process(input:string|null):Result {
  if(!input) return null;
  if(input.length===0) return emptyResult;
  if(!isValid(input)) throw new Error('invalid input');

  return compute(input);
}
```

## Comments

### Documentation Comments
**Use** JSDoc-style comments for functions and classes:
```typescript
/**
 * Calculates cashback for a transaction
 * @param trans - Transaction object or UUID
 * @param vcard - Optional vcard data
 * @returns Cashback amount or null
 */
async calcCashback(trans:Transaction|string, vcard?:IVcard|null) {
```

### Inline Comments
**Use** single-line comments for field descriptions:
```typescript
// ✅ Correct
static MAX_RETRIES = 3; // maximum retry attempts
const timeout = 5000; // milliseconds
/** Returns user by ID */
static getUserById = (id:string)=>users.get(id);
```

### Mixed Language Comments
**Allow** comments in native language (Ukrainian/Russian) when appropriate:
```typescript
// ✅ Acceptable
// обробка транзакції з кешбеком
const cashback = await calculateCashback(transaction);

/**
 * Sends notification to queue
 * відправляє нотифікацію в чергу обробки
 */
```

## Ternary Operators

### Inline Ternary
**Keep** ternary operators on single line:
```typescript
// ✅ Correct
const value = condition ? trueValue : falseValue;
const price = hasDiscount ? discountPrice : regularPrice;
const result = status==='active' ? process() : skip();

// ❌ Wrong - avoid breaking ternaries
const value = condition
  ? trueValue
  : falseValue;
```

## Static Properties

### Class Static Members
**Declare** static properties directly in class body:
```typescript
// ✅ Correct
export class CoreEvents {
  static S_NOTIFICATIONS = 'NOTIF';
  static S_CONFIRMATIONS = 'CONF';
  static Q_NOTIFICATION_SEND = CoreEvents.S_NOTIFICATIONS+'.send';

  static URL_FROM_QNAME = (qname:string)=>qname.replace(/^[A-Z.]+/, '');
}
```

## Imports and Exports

### Multi-Line Imports
**Group** related imports with minimal spacing:
```typescript
// ✅ Correct
import {
  IProduct,
  Product,
  ProductService,
  ProductType
} from '@tps-srv2/entity/product';

import { Controller, Get, Post, Body, Query } from '@nestjs/common';
```

## Debugging

### Console Statements
**Allow** console.log statements for debugging:
```typescript
// ✅ Acceptable in development
console.log('processing transaction:', trans.id);
console.warn('invalid state:', state);
console.error('failed to process:', error);
```

## General Principles

1. **Horizontal over Vertical**: Prioritize compact horizontal code over vertical expansion
2. **Minimal Spacing**: Reduce unnecessary whitespace around operators and keywords
3. **Inline Simple Logic**: Keep simple operations on single lines
4. **Type Inference**: Let TypeScript infer obvious types
5. **Early Returns**: Use guard clauses without braces
6. **Optional Chaining**: Leverage `?.` and `??` operators heavily
7. **Single Quotes**: Consistently use single quotes for strings
8. **Compact Formatting**: Favor readability through compactness

## Examples Summary

```typescript
// Complete example demonstrating style
export class PaymentService {
  static DEFAULT_TIMEOUT = 5000;

  constructor(
    private readonly db:DatabaseService,
    private readonly logger:Logger
  ) {}

  async processPayment(orderId:string, amount:number):Promise<PaymentResult|null> {
    if(!orderId) return null;
    if(amount<=0) throw new Error('invalid amount');

    const order = await this.db.getOrder(orderId); if(!order) return null;
    const total = order.items?.reduce((sum,item)=>sum+(item.price??0)*(item.qty??1), 0)??0;

    if(total!==amount) {
      this.logger.warn(`Amount mismatch: expected ${total}, got ${amount}`);
      return null;
    }

    const payment = await this.createPayment({orderId:orderId, amount:amount, status:'pending'});
    const result = await this.gateway.charge(payment.id, amount);

    if(result.success) return {payment:payment, status:'completed', transactionId:result.id};
    else return {payment:payment, status:'failed', error:result.error};
  }
}
```
