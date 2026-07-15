# ActionScript 3 / Adobe Flex Code Style Guide

This document defines ActionScript3 and MXML-specific code style used in the TooPro retail interface (tps_retail/swf). For cross-language patterns, see [general.md](general.md).

## Technology Stack

- **Language**: ActionScript 3.0
- **UI Framework**: Adobe Flex 4.x (Spark components)
- **Architecture**: PureMVC (Model-View-Controller)
- **Build**: Apache Flex SDK / Adobe Flex SDK
- **Communication**: AMF (Action Message Format) for client-server RPC

## File Structure

### File Types
- `.as` - ActionScript class files
- `.mxml` - Flex visual components (mix of XML and ActionScript)
- Package structure mirrors directory structure

### Package Declaration
**Always** match package to directory structure:

```actionscript
// ✅ Correct - file: src/model/ProductsProxy.as
package model {
    import org.puremvc.as3.patterns.proxy.Proxy;

    public class ProductsProxy extends Proxy {
        // implementation
    }
}
```

## Spacing and Formatting

### Control Structures
**No space** after control keywords (consistent with PHP and TypeScript style):

```actionscript
// ✅ Correct
if(condition) {
    doSomething();
}

for(var i:int = 0; i < items.length; i++) {
    process(items[i]);
}

while(running) {
    tick();
}

// ❌ Wrong
if (condition) {
    doSomething();
}
```

### Type Annotations
**No space** after colon in type annotations (compact style):

```actionscript
// ✅ Correct
private var spp:SellPlaceProxy;
public var timestamp:uint;
public function loadProducts():void {
    var fields:Array = ['title', 'price', 'avail'];
}

// ❌ Wrong
private var spp: SellPlaceProxy;
public var timestamp: uint;
```

### Function Declarations
Compact function signatures:

```actionscript
// ✅ Correct
public function filterProducts(str:String):ListCollectionView {
    if(!str.length) return new ListCollectionView(new ArrayList(prods));
    // ...
}

private function _loadedProducts(req:AMFProxyRequest):void {
    for each(var arrProd:Object in req.data) {
        prod = new ProductVO(arrProd, spp.sellPlaceID);
        prods.push(prod);
    }
}

// Multi-parameter functions
public function saveProduct(prodNID:uint, diffHash:Object):Boolean {
    // implementation
}
```

## Braces and Control Flow

### Opening Braces
**Always** place opening brace on same line (K&R style):

```actionscript
// ✅ Correct
public class ProductsProxy extends Proxy implements IProxy {
    public function loadProducts():void {
        if(condition) {
            // code
        }
    }
}

// ❌ Wrong
public class ProductsProxy extends Proxy implements IProxy
{
    public function loadProducts():void
    {
    }
}
```

### Single-Line Conditionals
**Omit braces** for single-line if/return statements:

```actionscript
// ✅ Correct
if(!spp) spp = facade.retrieveProxy(SellPlaceProxy.NAME) as SellPlaceProxy;
if(!req.data) return;
if(currNoteType != null) return;

// Multiple guards
if(!trans) return;
if(trans.value >= 50000) {
    showError('Transaction too large');
    return;
}

// ❌ Wrong - unnecessary braces
if(!trans) {
    return;
}
```

### if/else with single-statement branches — never use braces
When both `if` and `else` branches contain a single statement, write without braces. Each branch on its own line. Inline comments go at end of the line — **never** inside a block:

```actionscript
// ✅ Correct
if(t.type==OrderProxy.TT_SELL && ord.isWarrantySplitRequired()) _prepareSplitCheckout(t, ord);
else sendNotification(MVCConst.CART_CONFIRM, t, 'show box'); // No split needed

if(status==TS_FINISHED) sendNotification(MVCConst.TRANS_DONE, t);
else sendNotification(MVCConst.TRANS_PENDING, t);

// ❌ Wrong - expanding single-statement branches into blocks
if(t.type == OrderProxy.TT_SELL && ord.isWarrantySplitRequired()) {
    _prepareSplitCheckout(t, ord);
} else {
    // No split needed
    sendNotification(MVCConst.CART_CONFIRM, t, 'show box');
}
```

## Access Modifiers and Visibility

### Class Members
**Always** specify access level explicitly:

```actionscript
// ✅ Correct
public class ProductsProxy extends Proxy {
    public static const NAME:String = 'ProductsProxy';
    public static var i:ProductsProxy;

    public var timestamp:uint;
    public var repackRules:Object;

    private var spp:SellPlaceProxy;
    private var _sortInstance:ISort;
    private var _currFilterStr:String;

    public function loadProducts():void { }
    private function _loadedProducts(req:AMFProxyRequest):void { }
}
```

### Naming Convention for Private Members
**Use** underscore prefix for private members (especially methods and cached values):

```actionscript
// ✅ Correct
private var _sortInstance:ISort;
private var _imgListeners:Object = {};
private var _currFilterStr:String;

private function _loadedProducts(req:AMFProxyRequest):void { }
private function _onProdLoaded(req:AMFProxyRequest):void { }
private function _sortProducts(a:Object, b:Object, fields = null):int { }
```

## PureMVC Patterns

### Mediator "std init" Template
Every mediator starts with the same marked block: `NAME` const, one-line constructor, one-line typed `vw` getter. Reproduce it exactly when creating a new mediator:

```actionscript
// ✅ Correct
public class ConfirmBoxMediator extends Mediator implements IMediator {
    //std init start
    public static const NAME:String = "ConfirmBoxMediator";
    public function ConfirmBoxMediator(viewComponent:Object = null) { super(NAME, new ConfirmBoxFull()); init(); }
    public function get vw():ConfirmBoxFull {return viewComponent as ConfirmBoxFull;}  //view component
    //std init end
```

### One-Line Short Methods
Short methods and getters keep the whole body on the signature line:

```actionscript
// ✅ Correct
public function get vw():ConfirmBoxFull {return viewComponent as ConfirmBoxFull;}
public function get prods():Array { return data as Array; }
```

### Doc Comments on Private Fields
Non-obvious private vars get full `/** */` doc blocks explaining **why** the field exists:

```actionscript
// ✅ Correct
/**
 * True while box is being initialized (fillUp + addPopUp). Suppresses
 * PAY_TYPE_CHANGED notification during auto-commit of cashless DropDownList
 * (would cause spurious split-flip + retrigger loop).
 */
private var _initializing:Boolean = false;

/**
 * last edited field id, used to know which field was edited last,
 * used to try not to change its value when we have another field changed
 */
private var lastEditedField:String = 'numValue';
```

### Proxy Pattern
Standard structure for data proxies:

```actionscript
// ✅ Correct
package model {
    import org.puremvc.as3.interfaces.IProxy;
    import org.puremvc.as3.patterns.proxy.Proxy;

    public class ProductsProxy extends Proxy implements IProxy {
        public static const NAME:String = 'ProductsProxy';

        public function ProductsProxy() {
            super(NAME, new Array());
            i = this;
        }

        public static var i:ProductsProxy;

        // Getter for typed data access
        public function get prods():Array {
            return data as Array;
        }
    }
}
```

### Notifications
Send notifications to communicate between components:

```actionscript
// ✅ Correct
sendNotification(MVCConst.LD_PRODUCTS, this);
sendNotification(MVCConst.N_CONSOLE, {
    msg: 'Список товаров загружен успешно.',
    type: RFC3164.MSG_INFO
});
sendNotification(InitProcessCommand.CALL, this, 'products-loaded');
```

### Retrieving Proxies/Mediators
Standard facade pattern usage:

```actionscript
// ✅ Correct
var ap:AMFProxy = facade.retrieveProxy(AMFProxy.NAME) as AMFProxy;
var spp:SellPlaceProxy = facade.retrieveProxy(SellPlaceProxy.NAME) as SellPlaceProxy;
var settings:SettingsProxy = facade.retrieveProxy(SettingsProxy.NAME) as SettingsProxy;
```

## Type System

### Strong Typing
**Always** use strong typing for variables and parameters:

```actionscript
// ✅ Correct
public var timestamp:uint;
private var spp:SellPlaceProxy;
public function loadProducts():void { }
public function filterProducts(str:String):ListCollectionView { }

// Local variables
var prod:ProductVO;
var fields:Array;
var selectPrice:Boolean;
```

### Type Casting
Use `as` operator for safe casting:

```actionscript
// ✅ Correct
var spp:SellPlaceProxy = facade.retrieveProxy(SellPlaceProxy.NAME) as SellPlaceProxy;
var upd:UpdatesVO = req.data[MVCConst.N_UPDATES_IND] as UpdatesVO;
var rulesArr:Array = JSON.parse(req.data.toString()) as Array;
```

### Generic Collections
Specify types when possible:

```actionscript
// ✅ Correct
public function get prods():Array { return data as Array; }
var f:ListCollectionView = new ListCollectionView(new ArrayList(prods));
```

## Arrays and Objects

### Array Literals
Use bracket notation:

```actionscript
// ✅ Correct
var fields:Array = ['title', 'title2lng', 'price', 'avail'];
var statuses:Array = [
    {label: "Завершена", value: TS_FINISHED, color: 0x006400},
    {label: "Видалена", value: TS_DELETED, color: 0x8B0000}
];
```

### Object Literals
Inline objects are written **compact** (no space after colon) — dominant form in real code. Multiline notification objects may use a space after colon; both accepted:

```actionscript
// ✅ Correct — inline compact (dominant)
var statuses:Array = [
    {label:"Завершена", value:TS_FINISHED, color:0x006400},
    {label:"Видалена", value:TS_DELETED, color:0x8B0000}
];

// ✅ Also accepted — multiline spaced
sendNotification(MVCConst.N_CONSOLE, {
    msg: 'Error occurred',
    type: RFC3164.MSG_ERROR
});
```

### String Quotes
Single quotes preferred, but double quotes are common (~20% of the code, e.g. `NAME:String = "ConfirmBoxMediator"`, UI labels with Cyrillic text). Both accepted — don't convert existing ones.

### For-Each Loops
**Prefer** for-each for iterating collections:

```actionscript
// ✅ Correct
for each(var arrProd:Object in req.data) {
    prod = new ProductVO(arrProd, spp.sellPlaceID);
    prods.push(prod);
}

for each(var rule:Object in rulesArr) {
    repackRules[rule.source] = new RepackVO(rule);
}

// Traditional for loop when index needed
for(var i:int = 0; i < items.length; i++) {
    process(items[i]);
}
```

## Constants

### Naming
Use **UPPER_SNAKE_CASE** for constants:

```actionscript
// ✅ Correct
public static const NAME:String = 'ProductsProxy';
public static const TS_FINISHED:int = 100;
public static const TS_DELETED:int = -5;
public static const TS_PREORDER:int = 5;
```

### Grouping
Group related constants in a class:

```actionscript
// ✅ Correct
public class TransactionsProxy extends Proxy {
    // Transaction statuses
    public static const TS_FINISHED:int = 100;
    public static const TS_NDEFERR:int = -1;
    public static const TS_DELETED:int = -5;
    public static const TS_PREORDER:int = 5;
    public static const TS_ORDERED:int = 10;
    public static const TS_PAYED:int = 20;
}
```

## Comments

### Documentation Comments
Use JSDoc-style comments for public methods:

```actionscript
// ✅ Correct
/**
 * Load products data ARRAY from server.
 * LD_PRODUCTS notification will be sent after receiving products.
 */
public function loadProducts():void {
    // implementation
}

/**
 * Returns NEW filtered by title 'array' of products.
 * Used to filter long list of products in data grid.
 */
public function filterProducts(str:String):ListCollectionView {
    // implementation
}
```

### Inline Comments
Use single-line comments for implementation notes:

```actionscript
// ✅ Correct
// clear current prods
prods.splice(0, prods.length);

// save to data array
for each(var arrProd:Object in req.data) {
    prod = new ProductVO(arrProd, spp.sellPlaceID);
    prods.push(prod);
}

// tell app that products was loaded
sendNotification(MVCConst.LD_PRODUCTS, this);
```

### Mixed Language Comments
**Allow** Ukrainian/Russian in comments:

```actionscript
// ✅ Acceptable
// проблема в том, что транзакция содержит ссылки на ProductVO в массиве товаров
var ba:ByteArray = new ByteArray();
ba.writeObject(trans.data.products);

// если сейчас статус больше или равен "оплачен"
if(status >= TPSTrans::S_PAYED && oldStatus < TPSTrans::S_PAYED) {
    // delta debt value of the user
    upd.users[trans.usr_they].debt = trans.data.debt;
}
```

## Error Handling

### Early Returns
**Favor** early returns for validation:

```actionscript
// ✅ Correct
public function send(trans:TransactionVO):void {
    if(currNoteType != null) return;

    lastTransaction = trans;

    // block transactions >50k
    if(trans.value >= 50000 && trans.type == OrderProxy.TT_SELL) {
        sendNotification(MVCConst.N_CONSOLE, {
            type: RFC3164.MSG_ERROR,
            msg: 'Транзакция не может быть больше 50 тыс.'
        });
        return;
    }

    AMFProxy.instance.newreq('transaction', 'add').listen(_transactionSent).call(trans);
}
```

### Null Checks
Check for null/undefined before use:

```actionscript
// ✅ Correct
if(!req.data) return;
if(!spp) spp = facade.retrieveProxy(SellPlaceProxy.NAME) as SellPlaceProxy;

// Check properties
if(!product.uuid) {
    trace('no uuid for product ' + product.nid);
    return;
}
```

## MXML Components

### Component Structure
Standard MXML component structure:

```xml
<!-- ✅ Correct -->
<?xml version="1.0" encoding="utf-8"?>
<s:Group xmlns:fx="http://ns.adobe.com/mxml/2009"
    xmlns:s="library://ns.adobe.com/flex/spark"
    xmlns:mx="library://ns.adobe.com/flex/mx"
    height="18"
    width="10">

    <fx:Declarations>
        <fx:uint id="dotColor">0x8B8B8B</fx:uint>
        <fx:Number id="fillAlpha">0.2</fx:Number>
    </fx:Declarations>

    <s:Rect height="100%" width="100%">
        <s:fill>
            <s:SolidColor color="{fillColor}" alpha="{fillAlpha}"/>
        </s:fill>
    </s:Rect>

</s:Group>
```

### Data Binding
Use curly braces for data binding:

```xml
<!-- ✅ Correct -->
<s:Rect height="2" width="2" left="2" top="2">
    <s:fill>
        <s:SolidColor color="{dotColor}"/>
    </s:fill>
</s:Rect>

<s:Label text="{product.title}" visible="{showDetails}"/>
```

### Indentation in MXML
Use **tabs** for indentation, align attributes vertically when multiple:

```xml
<!-- ✅ Correct -->
<s:Rect height="2"
    width="2"
    left="6"
    top="14">
    <s:fill>
        <s:SolidColor color="{dotColor}"/>
    </s:fill>
</s:Rect>
```

## AMF Communication

### AMF Request Pattern
Standard pattern for server communication:

```actionscript
// ✅ Correct
// Using AMFProxy
AMFProxy.instance.newreq('node', 'tps_range')
    .listen(_loadedProducts)
    .call('type', 'product', fields);

// With ID tracking
AMFProxy.instance.newreq('node', 'tps_save')
    .setID(prodNID.toString())
    .listen(_onSaveProd)
    .call(diffHash);

// Multiple parameters
AMFProxy.instance.newreq('transaction', 'add')
    .listen(_transactionSent)
    .call(trans, timestamp);
```

### Response Handlers
Private methods with descriptive names:

```actionscript
// ✅ Correct
private function _loadedProducts(req:AMFProxyRequest):void {
    if(!req.data) return;

    // process data
    for each(var arrProd:Object in req.data) {
        prod = new ProductVO(arrProd, spp.sellPlaceID);
        prods.push(prod);
    }

    // send notifications
    sendNotification(MVCConst.LD_PRODUCTS, this);
}

private function _onProdLoaded(req:AMFProxyRequest):void {
    var p:ProductVO = getProductByProperty(parseInt(req.id), 'nid');
    if(p) p.updateData(req.data);
}
```

## Value Objects (VOs)

### VO Structure
Clean data transfer objects:

```actionscript
// ✅ Correct
package model.vo {
    public class ProductVO {
        public var nid:uint;
        public var title:String;
        public var price:Array;
        public var avail:Array;
        public var uuid:String;

        public function ProductVO(data:Object, placeDID:uint) {
            // populate from data
            this.nid = data.nid;
            this.title = data.title;
            this.price = data.price;
            this.avail = data.avail;
        }
    }
}
```

## Debugging

### Trace Statements
**Allow** trace() for debugging:

```actionscript
// ✅ Acceptable in development
trace('prod image req for ' + product.nid + ' uuid=' + product.uuid);
trace('Product image request for ' + product.nid);
trace(pInfo.img);
trace('why error?');
trace(err);
```

### Conditional Traces
Use for development debugging:

```actionscript
// ✅ Acceptable
if(pInfo.cached == '0') trace(pInfo);
if(DEBUG_MODE) trace('Processing: ' + item.id);
```

## Vertical Alignment

Extra spaces aligning related statements into columns are deliberate — preserve them:

```actionscript
// ✅ Correct
if(this._bonusEnabled) ret -= vw.bonusComponent.getFinalValueToUse();
if(this._allowDebt)    ret -= vw.numDebt.value;
```

## File-Top Design Notes

Files may open with a block comment (often Russian/Ukrainian) explaining the class's role and usage patterns — e.g. `ConfirmBoxMediator.as` explains how mediators work. Preserve these.
