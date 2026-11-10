# TypeScript Code Style Guide

TypeScript/NestJS specifics for the TpsSrv2 monorepo. The core formatting rules (keyword spacing, type-annotation colons, brace-less single statements, quotes, long lines, `?.`/`??`, one-line guards, banners, comment markers, alignment) and the either-form table (comparison spacing, arrows, object literals, constructor DI, `switch`) are in `SKILL.md` and are not repeated here. Comment and naming habits are in [general.md](general.md).

## Statements on One Line

Short related statements may share a line, separated by semicolons:
```typescript
// ✅ Correct
if(value<=0) value = 0; else value = Math.round(value * 100) / 100;
if(typeof result==='string') return result; else await wait();

// ✅ Correct - fetch + guard
const id = getId(item); if(!id) return false;
```

## Type Annotations

### Generics, Function Types, Local Variables
The compact colon rule also covers generic parameters, function types and local variables:
```typescript
// ✅ Correct
function process<T>(items:T[], mapper:(item:T)=>string):string[] {
  const result:Map<string,T> = new Map();
  const value:string|number|null = getValue();
}

interface TerminalConfig {
  name:string;
  host:string;
  port:number;
}

// ❌ Wrong
function process<T>(items: T[], mapper: (item: T) => string): string[] {
  const result: Map<string, T> = new Map();
}
```

### Union Types Inline
**Keep** union types on a single line without breaks:
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

### Type Inference Preferred
**Allow** TypeScript to infer types when obvious:
```typescript
// ✅ Correct
const count = 0;
const items = new Map<string,Product>();
const result = await fetchData();

// ❌ Unnecessary - type is obvious
const count:number = 0;
```

### Constructor DI
Constructor DI parameters are usually written with a space after the colon (either form is fine, see `SKILL.md`). Short abbreviated names for injected services are accepted and common: `srvc`, `lgr`.
```typescript
constructor(
  private readonly coinService: CoinService,
  private readonly lgr: TpsLogger
) {
  this.lgr.setContext('c6k.spend');
}
```

## Method Chaining

Chain methods on a **single line** when reasonable, or on **minimal lines**:
```typescript
// ✅ Correct - short chain on one line
const query = dbqb<Product>().fields(['id','name']).sort(['-created']).limit(10);

// ✅ Correct - longer chain with minimal lines
const query = dbqb<Product>()
  .equal('status', 'active')
  .equal('type', ProductType.RETAIL)
  .fields(['id','name','price']).limit(100).sort('-created');
```

## Strings

Use backticks for interpolation, single quotes otherwise:
```typescript
console.log(`Processing item ${id} with status ${status}`);
const url = `${baseUrl}/api/${endpoint}`;
```

## Objects

**Keep** short objects on a single line:
```typescript
// ✅ Correct
const point = {x: 10, y: 20};

// ❌ Wrong - unnecessary line breaks for short objects
const point = {
  x: 10,
  y: 20
};
```

## Ternary Operators

**Keep** ternary operators on a single line:
```typescript
// ✅ Correct
const price = hasDiscount ? discountPrice : regularPrice;
const result = status==='active' ? process() : skip();

// ❌ Wrong - avoid breaking ternaries
const value = condition
  ? trueValue
  : falseValue;
```

## Comments

Doc block form for functions and classes:
```typescript
/**
 * Calculates cashback for a transaction
 * @param trans - Transaction object or UUID
 * @param vcard - Optional vcard data
 * @returns Cashback amount or null
 */
async calcCashback(trans:Transaction|string, vcard?:IVcard|null) {
```

Field and constant descriptions go on the same line or in a one-line doc block:
```typescript
static MAX_RETRIES = 3; // maximum retry attempts
/** Returns user by ID */
static getUserById = (id:string)=>users.get(id);
```

## Static Members

**Declare** static properties directly in the class body:
```typescript
export class CoreEvents {
  static S_NOTIFICATIONS = 'NOTIF';
  static Q_NOTIFICATION_SEND = CoreEvents.S_NOTIFICATIONS+'.send';

  static URL_FROM_QNAME = (qname:string)=>qname.replace(/^[A-Z.]+/, '');
}
```

## Imports

Long import lists go one name per line; short ones stay on one line:
```typescript
import {
  IProduct,
  Product,
  ProductService,
  ProductType
} from '@tps-srv2/entity/product';

import { Controller, Get, Post, Body, Query } from '@nestjs/common';
```

## Debugging

`console.log` / `console.warn` / `console.error` are allowed for debugging:
```typescript
console.log('processing transaction:', trans.id);
```

## NestJS Controller Patterns

Boolean query params come in as string flags, compared explicitly:
```typescript
@Get('connect')
async connect(
  @Query('host') host:string,
  @Query('port', ParseIntPipe) port:number,
  @Query('force') forceStr = '0',
  @Query('name') name = 'default'
) {
  return await this.srvc.connect(host, port, forceStr === '1', name);
}
```

## Complete Example

```typescript
export class PaymentService {
  static DEFAULT_TIMEOUT = 5000;

  constructor(
    private readonly db: DatabaseService,
    private readonly logger: Logger
  ) {}

  //////////////////////////////////////////////////////////
  // PAYMENT METHODS

  async processPayment(orderId:string, amount:number):Promise<PaymentResult|null> {
    if(!orderId) return null;
    if(amount<=0) throw new Error('invalid amount');

    const order = await this.db.getOrder(orderId); if(!order) return null;
    const total = order.items?.reduce((sum,item)=>sum+(item.price??0)*(item.qty??1), 0)??0;
    if(total!==amount) {this.logger.warn(`Amount mismatch: expected ${total}, got ${amount}`); return null;}

    const payment = await this.createPayment({orderId, amount, status: 'pending'});
    const result = await this.gateway.charge(payment.id, amount);

    if(result.success) return {payment, status: 'completed', transactionId: result.id};
    else return {payment, status: 'failed', error: result.error};
  }
}
```
