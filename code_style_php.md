# PHP Code Style Guide (TooPro/Drupal)

This document defines PHP-specific code style used in the TooPro Drupal 7 project. For cross-language patterns, see [code_style_general.md](code_style_general.md).

## PHP Version and Framework

- **PHP Version**: 5.3+ (Drupal 7 compatible)
- **Framework**: Drupal 7.x with custom modules
- **Database Access**: Drupal Database API and direct MySQL queries

## File Structure

### PHP Tags
**Always** use full PHP opening tags, leave closing tags on pure PHP files:

```php
// ✅ Correct - .module, .inc, .php files
<?php

function my_module_function() {
    // code
}
// No closing ?> tag at end of file
```

### File Types
- `.module` - Drupal module hooks and main functionality
- `.inc` - Include files with helper functions
- `.class.php` - Class definitions
- `.install` - Installation/update hooks
- `.admin.inc` - Administrative interface functions

## Spacing and Formatting

### Control Structures
**No space** after control keywords, but **do use** spaces inside parentheses sparingly:

```php
// ✅ Correct
if($condition) {
    doSomething();
}

foreach($items as $item) {
    process($item);
}

while($running) {
    tick();
}

// ❌ Wrong
if ($condition) {
    doSomething();
}
```

### Operators
**Minimal** spacing around operators (different from TypeScript compact style, PHP tends to have more spacing):

```php
// ✅ Correct
$result = $a + $b;
$value = $price * $amount;
if($x == $y) return true;
if($status === 'active') process();

// Also acceptable for complex expressions
$deltaWage = (($trans->value + @$trans->data->debt) * $delMinus) * ($currPerc * 0.01);
```

### Function Calls and Definitions
Compact style with minimal spacing:

```php
// ✅ Correct
function processData($input, $options=array()) {
    $result = calculate($input);
    return format($result, $options);
}

// Function calls
$value = TPSCore::getFieldValue('user', $uid, 'phone', 0);
node_save($node);
```

## Braces and Control Flow

### Opening Braces
**Always** place opening brace on same line (K&R style):

```php
// ✅ Correct
class MyClass {
    public function method() {
        if($condition) {
            // code
        }
    }
}

// ❌ Wrong
class MyClass
{
    public function method()
    {
    }
}
```

### Single-Line Conditionals
**Omit braces** for single-line if/else/return statements:

```php
// ✅ Correct
if(!$id) return null;
if(!$data) throw new Exception('missing data');
if($value <= 0) $value = 0; else $value = round($value * 100) / 100;

// Multiple related guards
if(!$id) return null;
if(!is_numeric($id)) return services_error('Wrong user ID');
const $result = process($id);

// ❌ Wrong - unnecessary braces
if(!$id) {
    return null;
}
```

## Arrays

### Array Declaration
Use **old-style array()** syntax (Drupal 7 / PHP 5.3 compatible):

```php
// ✅ Correct
$items = array('item1', 'item2', 'item3');
$config = array(
    'host' => 'localhost',
    'port' => 3000,
    'timeout' => 5000
);

// ❌ Wrong (PHP 5.4+ short syntax - avoid for Drupal 7)
$items = ['item1', 'item2', 'item3'];
```

### Associative Arrays
No space after `=>` operator:

```php
// ✅ Correct
$form['sellplace'] = array(
    '#type'=> 'fieldset',
    '#title'=> 'Sell Place'
);

$items = array(
    'title'=> 'TooPro Shop Config',
    'page callback'=> 'drupal_get_form',
    'page arguments'=> array('tps_core_config_form'),
    'access arguments'=> array('administer')
);
```

## Drupal-Specific Patterns

### Hook Implementations
Follow Drupal naming: `modulename_hookname`:

```php
// ✅ Correct
function tps_core_menu() {
    $items = array();
    $items['admin/config/system/tps'] = array(
        'title'=> 'TooPro Shop Config',
        'page callback'=> 'drupal_get_form'
    );
    return $items;
}

function tps_core_permission() {
    return array(
        'edit ANY user'=> array(
            'title'=> t('Edit info about any user'),
            'restrict access'=> TRUE
        )
    );
}
```

### Form API
Use Drupal Form API conventions:

```php
// ✅ Correct
function tps_core_config_form($form, &$form_state) {
    $form['sellplace'] = array(
        '#type'=> 'fieldset',
        '#title'=> 'Sell Place'
    );
    $form['sellplace']['tps_curr_place'] = array(
        '#type'=> 'select',
        '#title'=> 'Select sell place',
        '#default_value'=> variable_get('tps_curr_place', 0),
        '#options'=> $options
    );
    return system_settings_form($form);
}
```

### Database Queries

**Prefer** Drupal Database API for queries:

```php
// ✅ Correct - Using db_select
$result = db_select('node', 'n')
    ->fields('n', array('nid', 'title'))
    ->condition('type', 'product')
    ->execute()
    ->fetchAll();

// ✅ Correct - Using db_query for complex queries
$result = db_query("SELECT * FROM {tps_transaction} WHERE status >= :status",
    array(':status'=> TPSTrans::S_PAYED)
);

// Building complex query strings
$where = array();
if($filterObj->dateFrom) $where['dateFrom'] = " `created` >= {$filterObj->dateFrom} ";
if($filterObj->dateTo) $where['dateTo'] = " `created` <= {$filterObj->dateTo} ";
$q = "SELECT * FROM {tps_transaction}";
if(count($where)) $q = $q . ' WHERE (' . implode(' AND ', $where) . ') ';
```

## Classes

### Class Naming
Use **PascalCase**, often prefixed with project identifier:

```php
// ✅ Correct
class TPSCore {
    const UR_BUYER = 'Buyer';
    public static $instance = NULL;

    public static function currPlaceNID() {
        return TPSCore::placeNID(TPSCore::currPlaceDID());
    }
}

class TPSRetailSettings {
    public $sellerNames = 'Прод.1, Прод.2';
    public $defaultVcard = '';
}
```

### Static Methods
Use **public static** for utility methods:

```php
// ✅ Correct
class TPSCore {
    public static function floatsEqual($a, $b) {
        if(abs($a - $b) < 0.0001) return TRUE;
        else return FALSE;
    }

    public static function getFieldValue($bundle, $id, $fname, $delta=-1) {
        $entType = ($bundle == 'user') ? 'user' : 'node';
        // ...
        return $ret;
    }
}

// Usage
if(TPSCore::floatsEqual($value1, $value2)) { }
$phone = TPSCore::getFieldValue('user', $uid, 'phone', 0);
```

### Singleton Pattern
Common pattern for service classes:

```php
// ✅ Correct
class TPSTransAPI {
    static function i() {
        if(self::$instance == NULL) {
            self::$instance = new TPSTransAPI();
        }
        return self::$instance;
    }
    static private $instance = NULL;
}

// Usage
$trans = TPSTransAPI::i()->range($filter);
```

## Error Handling

### Drupal Services Errors
Use `services_error()` for API endpoints:

```php
// ✅ Correct
if(!is_numeric($id)) return services_error('Wrong user ID', 400, array($id));
if(!$bundle) return services_error('No such vocabulary ' . $vocName);
```

### Watchdog Logging
Use Drupal's `watchdog()` for logging:

```php
// ✅ Correct
watchdog('TPS', "Trans !tid added: value @val", array(
    '!tid'=> $tLink,
    '@val'=> $transaction->value
), WATCHDOG_INFO);

watchdog('tps_retail', "ERR trans ADD: <br><b>$res</b><pre>" . print_r($transaction, 1) . '</pre>');
```

### Return Early Pattern
**Favor** early returns for validation:

```php
// ✅ Correct
function tps_core_normalize_phone($phone) {
    // regex replace all except numbers
    $phone = preg_replace('/[^0-9]/', '', $phone);

    // fix phones with old format
    if(strlen($phone) == 13 && substr($phone, 0, 4) == '0380') $phone = substr($phone, 1);
    if(strlen($phone) == 9) $phone = '0' . $phone;
    if(strlen($phone) == 10) $phone = '38' . $phone;

    // validation
    if(strlen($phone) != 12) $phone = 0;
    if(substr($phone, 0, 1) == '0') $phone = 0;

    return $phone;
}
```

## Comments

### PHPDoc Comments
**Use** PHPDoc format for functions and classes:

```php
// ✅ Correct
/**
 * Apply updates to DB using delta values received in $updatesVO.
 *
 * @param UpdatesVO $updatesVO
 * @param boolean   $emulate if TRUE - new UpdatesVO will be returned, but NOT saved to db
 * @param boolean   $saveProgress if TRUE - progress will be saved to file
 * @return UpdatesVO with final values received after clearing given transactions
 */
function tps_core_apply_delta_updates(&$updatesVO, $emulate=FALSE, $saveProgress=FALSE) {
    // implementation
}

/**
 * to run cron every 5 minutes:
 * ```
 * crontab -e
 * /5 * * * * wget --spider http://obol.nfeya.my/cron.php
 * ```
 * @param $queueName string one of predefined queues
 * @param $data
 * @return void
 */
function tps_core_add_cron_job($queueName, $data) {
    // implementation
}
```

### Inline Comments
Use single-line comments for explanations:

```php
// ✅ Correct
// если сейчас статус больше или равен "оплачен"
if($status >= TPSTrans::S_PAYED && $oldStatus < TPSTrans::S_PAYED) {
    // delta debt value of the user
    $upd->users->{$trans->usr_they}->debt = round($trans->data->debt, 2) * $delMinus;
}

// get drupal user account object (load if it exist or create new dummy user obj)
if($node_edits->nid) {
    $node = node_load($node_edits->nid);
} else {
    $node = new stdClass();
}
```

## Variable and Property Access

### Object Property Access
Use **arrow notation** `->`:

```php
// ✅ Correct
$node->title = 'New Title';
$trans->id = TPSTransAPI::IDintTOstr($trans->id);
$upd->products->{''.$prod->nid} = $deltaProd;
```

### Error Suppression
**Allow** `@` operator when appropriate:

```php
// ✅ Correct
$deltaWage = (($trans->value + @$trans->data->debt) * $delMinus) * ($currPerc * 0.01);
if($cache = cache_get('tps_core_addphotos', 'cache')) {
    $bprod = $cache->data['bprod'];
}
```

## String Handling

### Quotes
**Use single quotes** for simple strings, double quotes for interpolation:

```php
// ✅ Correct
$message = 'Hello world';
$path = 'api/users';
throw new Error('invalid input');

// Double quotes for string interpolation
$error = "No node with nid $nid found";
$log = "Trans $transId added by user $uid";
```

### String Concatenation
Use `.` operator with spacing:

```php
// ✅ Correct
$url = $settings->orderTableExecLink;
$url .= '?act=addProduct';
$url .= '&pID=' . $nid;
$url .= '&pcs=' . $amount;
$url .= '&pName=' . urlencode($title);
```

## Type Checking and Casting

### Type Checks
Use appropriate type checking functions:

```php
// ✅ Correct
if(is_numeric($val)) $val = intval($val);
if(is_array($values)) { }
if(is_string($result)) return services_error($result);
if(is_object($transId)) $transId = $transId->id;
```

### Type Casting
Explicit casting when needed:

```php
// ✅ Correct
$placeDID = intval(variable_get('tps_curr_place', 0));
$ret = 0 + floatval($req->execute()->fetchField());
$data = (array) json_decode($result);
$transaction->data = (object) $transaction->data;
```

## Drupal Variable System

### Getting/Setting Variables
Use Drupal's `variable_get()` and `variable_set()`:

```php
// ✅ Correct
$placeId = variable_get('tps_curr_place', 0);
$dollar = variable_get('tps_cost_dollar', '');

variable_set('tps_curr_place_id', $id);
```

## Comparison with Standard PHP Coding Styles

### Differences from PSR-2/PSR-12
This codebase differs from PSR standards in several ways:

1. **No space after control keywords**: `if($x)` instead of `if ($x)`
2. **No space after =>**: `'key'=> 'value'` instead of `'key' => 'value'`
3. **Omit braces for single-line statements**
4. **Old array() syntax** (Drupal 7 / PHP 5.3 compatibility)
5. **Tabs or spaces** (project-specific, not strictly enforced)

These differences serve the compact, pragmatic style of the codebase.

## Best Practices

### Module Organization
1. Keep hook implementations in `.module` file
2. Put helper functions in `.inc` files
3. Put classes in `.class.php` files
4. Use `module_load_include()` to load additional files

```php
// ✅ Correct
module_load_include('inc', 'tps_core', 'rare_funcs');
tps_core_rarefunc_remove_not_used_users($dryRun);
```

### Function Naming
- Module hooks: `modulename_hookname`
- Helper functions: `modulename_descriptive_name` or `_modulename_helper_name` (underscore prefix for private)
- Class methods: `camelCase`

### Performance Considerations
- Cache expensive operations
- Use Drupal's cache system
- Minimize database queries
- Use static variables for repeated calls

```php
// ✅ Correct - Caching
if($cache = cache_get('tps_core_addphotos', 'cache')) {
    $bprod = $cache->data['bprod'];
} else {
    // expensive operation
    cache_set('tps_core_addphotos', array('bprod'=> $bprod), time() + 60 * 60 * 24);
}
```

## Conclusion

This PHP style guide reflects the Drupal 7 era conventions mixed with compact, practical patterns developed for the TooPro project. When in doubt:

1. Follow existing patterns in the module
2. Maintain Drupal 7 compatibility
3. Prioritize working code over style purity
4. Document complex logic clearly
