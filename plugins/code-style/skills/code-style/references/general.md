# General Code Style Guide

This document defines cross-language code style patterns used across the TooPro project (PHP, ActionScript3, and TypeScript codebases). These patterns represent the common philosophy and approach to coding that should be maintained regardless of the programming language.

## Language-Specific Guides

This general guide is supplemented by language-specific style guides:
- **TypeScript**: [typescript.md](typescript.md) - TypeScript/NestJS code style
- **PHP**: [php.md](php.md) - PHP/Drupal code style
- **ActionScript3**: [as3.md](as3.md) - ActionScript3/Flex code style

## Core Principles

### 1. Practical Over Theoretical
- Favor working solutions over architectural purity
- Prioritize getting things done over perfect abstraction
- Allow mixed approaches when they serve the business need

### 2. Minimal Spacing Philosophy
- Reduce unnecessary whitespace around operators and keywords
- Prefer compact horizontal code over excessive vertical expansion
- Use spacing strategically to improve readability, not by rigid rules

### 3. Direct Communication
- Variable and function names should be clear and descriptive
- Comments should explain **why**, not **what**
- Use native language (Ukrainian/Russian) in comments when more natural for the team

## Comments and Documentation

### Function Documentation

**Always** provide documentation for public functions/methods with:
- Brief description of what the function does
- Parameter descriptions with types
- Return value description
- Usage examples for complex functions

```php
// PHP example
/**
 * Apply updates to DB using delta values received in $updatesVO
 * @param UpdatesVO $updatesVO
 * @param boolean   $emulate if TRUE - new UpdatesVO with final updated values will be returned, but NOT saved to db
 * @return UpdatesVO with final values received after clearing given transactions
 */
function tps_core_apply_delta_updates(&$updatesVO, $emulate=FALSE) {
```

```actionscript
// ActionScript3 example
/**
 * Returns NEW filtered by title 'array' of products.
 * Used to filter long list of products in data grid.
 */
public function filterProducts(str:String):ListCollectionView {
```

### Inline Comments

**Use** single-line comments for:
- Field/constant descriptions
- Complex logic explanations
- Important notes and warnings

```php
// ✅ Correct
$timeout = 5000; // milliseconds
// fix phones with old format
if(strlen($phone)==13 && substr($phone, 0, 4) == '0380') $phone = substr($phone, 1);
```

```actionscript
// ✅ Correct
public var timestamp:uint;  // Last time app received available product values from server
```

### Comment Markers

- `//TODO` — written without a space after `//` (project convention, 23+ occurrences)
- `//!!fut`, `//!!future`, `//!!maybe` — project markers for "future improvement / open question"; preserve them exactly when editing nearby code
- Short inline notes may omit the space after `//` (`//validate`, `return x;//reason`) — accepted, don't reformat

### File-Top Design Notes

**Allow** (and preserve) large doc blocks at the top of a file describing the overall algorithm or design decisions, often in Ukrainian/Russian. Example: `tps_pay_method.module` opens with a numbered fiscalization algorithm. These are documentation, not dead comments — never strip them.

### Commented-Out Code — Keep With Reason

Commented-out blocks are kept when they document a decision. Always attach the reason:

```php
/*'cron'=> array( //dont used, because receip_check do the cron job when needed
    ...
),*/
```

### Closing-Bracket Trailing Markers

On deeply nested structures, a trailing comment marks what the bracket closes:

```php
    ))//rro
);
```

### Mixed Language Comments

**Allow** comments in native language (Ukrainian/Russian) alongside English:
- Use English for public API documentation
- Use native language for internal implementation notes
- Mix languages naturally based on team communication patterns

```php
// ✅ Acceptable
// обробка транзакції з кешбеком
$cashback = await calculateCashback($transaction);

/**
 * Sends notification to queue
 * відправляє нотифікацію в чергу обробки
 */
```

## Naming Conventions

### Classes and Types
- Use **PascalCase** for class names
- Prefix project-specific classes with project identifier when appropriate
- Value Objects often have `VO` suffix

```php
class TPSCore { }
class ProductVO { }
class UpdatesVO { }
```

```actionscript
public class ProductsProxy extends Proxy { }
public class TransactionVO { }
```

### Functions and Methods
- Use **camelCase** for function/method names
- Start with verb describing the action
- Be descriptive, avoid abbreviations unless very common

```php
// ✅ Correct
function tps_core_normalize_phone($phone) { }
public static function getFieldValue($bundle, $id, $fname) { }
```

```actionscript
// ✅ Correct
public function loadProducts():void { }
public function filterProducts(str:String):ListCollectionView { }
```

### Variables
- Use **camelCase** for local variables
- Use **snake_case** for database-related variables (PHP)
- Be descriptive - single letter variables only for simple loops

```php
// ✅ Correct
$currPerc = TPSCore::getFieldValue('user', $trans->usr_us, 'percent', 0);
$phone_number = '380661112233';
for($i=0; $i<count($items); $i++) { }
```

```actionscript
// ✅ Correct
private var currNoteType:String;
for(var i:int=0; i<items.length; i++) { }
```

### Constants
- Use **UPPER_SNAKE_CASE** for constants
- Group related constants in a class/namespace
- Use descriptive names that convey meaning

```php
// ✅ Correct
define('MSG_EMERGENCY', 0);
define('MSG_ALERT', 1);
const UR_BUYER = 'Buyer';
```

```actionscript
// ✅ Correct
public static const NAME:String = 'ProductsProxy';
public static const TS_FINISHED:int = 100;
```

## Code Organization

### File Structure
- One primary class per file (exceptions allowed for small helper classes)
- Group related functionality in modules/packages
- Keep files focused on a single responsibility

### Function Length
- Keep functions focused and relatively short (typically 20-100 lines)
- Long functions are acceptable if they handle a single logical flow
- Break up functions only when it improves clarity

### Class Structure Order
1. Constants
2. Static properties
3. Instance properties
4. Constructor
5. Public methods
6. Protected/private methods
7. Static methods

```php
class Example {
    // Constants
    const MAX_RETRIES = 3;

    // Static properties
    public static $instance = NULL;

    // Instance properties
    private $data;

    // Constructor
    public function __construct() { }

    // Public methods
    public function process() { }

    // Private methods
    private function validate() { }

    // Static methods
    public static function getInstance() { }
}
```

## Error Handling

### Early Returns
**Favor** early returns for validation and error cases:

```php
// ✅ Correct
function process($input) {
    if(!$input) return null;
    if(!isValid($input)) return false;

    return doProcessing($input);
}
```

```actionscript
// ✅ Correct
public function send(trans:TransactionVO):void {
    if(!trans) return;
    if(trans.value >= 50000) {
        showError('Transaction too large');
        return;
    }

    processTransaction(trans);
}
```

### Error Messages
- Use clear, descriptive error messages
- Include relevant context (IDs, values) in messages
- Log errors with appropriate severity levels

```php
// ✅ Correct
if(!$node) return services_error('No node with such nid.');
watchdog('TPS', "Trans !tid added", array('!tid' => $tLink), WATCHDOG_INFO);
```

## Data Structures

### Arrays and Objects
- Use arrays for ordered collections
- Use objects/hashes for key-value associations
- Be explicit about expected structure in function parameters

```php
// Arrays for collections
$products = array($prod1, $prod2, $prod3);

// Objects for key-value data
$updates = (object)array(
    'products' => $productUpdates,
    'users' => $userUpdates
);
```

```actionscript
// Arrays for collections
var items:Array = [item1, item2, item3];

// Objects for key-value data
var config:Object = {
    timeout: 5000,
    retries: 3
};
```

### Type Hints and Annotations
- Always specify types in function signatures (when language supports)
- Document expected types in comments for dynamic languages
- Use strict typing when available

## Debugging

### Console/Trace Statements
**Allow** debug statements during development:
- Use appropriate logging levels
- Include context in log messages
- Remove or comment sensitive information before production

```php
// ✅ Acceptable in development
watchdog('tps_retail', "Processing transaction: $trans->id", null, WATCHDOG_DEBUG);
```

```actionscript
// ✅ Acceptable in development
trace('Product image request for ' + product.nid);
```

### Commented Debug Code
**Allow** commented debug code if it might be useful:

```php
// Kept for future debugging
//echo "tps core subm"; print_r($node); die();
```

## Formatting Flexibility

### Line Length
- No strict line length limit
- Prefer keeping related code on one line if readable
- Break lines when it improves comprehension, not by character count

### Indentation
- Use **tabs** or **spaces** consistently within a project
- PHP: Typically 4 spaces or 1 tab
- ActionScript3: Typically 1 tab
- TypeScript: See specific guide

### Blank Lines
- Use blank lines to separate logical blocks
- One blank line between functions
- Use sparingly within functions to group related statements

### Vertical Alignment (deliberate)
Extra spaces/tabs used to align related lines into columns are **intentional** — never collapse them:

```php
'help'		=> 'Check status...',
'file'		=> array('type'=>'inc', 'module'=>'tps_pay_method'),
'callback'	=> '_tpsrr_rro_works',
```

```actionscript
if(this._bonusEnabled) ret -= vw.bonusComponent.getFinalValueToUse();
if(this._allowDebt)    ret -= vw.numDebt.value;
```

### Section Banner Comments
Group related methods with banner comments (used across TS services/controllers and AS3 mediators):

```typescript
//////////////////////////////////////////////////////////
// CONNECT METHODS
```

## When in Doubt

1. Follow existing patterns in the file you are editing
2. Prioritize clarity and correctness
3. Never mass-reformat surrounding code while making a change
