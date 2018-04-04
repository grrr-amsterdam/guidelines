# General coding standards

This document contains the preferred coding style regardless of language. The examples below will be in PHP, but the accompanying rules work for Javascript or Ruby as well.

## Whitespace
At its most basic, we generally prefer to not cram everything together.
Use whitespace around parentheses, variables, and operators.
Example:

```php
class Garp_FiddleStick {
    public function __construct($foo, $bar = null) {
        if ($foo >= 10000) {
            doSomething();
        }
        $this->_property = $foo;
        
        if ($bar) {
            $this->_id = 1000 + $bar;
        }
    }

    public function loopDeLoop() {
        for ($i = 0; $i < count($this->_data); ++$i) {
            dump($this->_data[$i]);
        }
    }
}
```

## Indentation
We use spaces for indentation. 
* 2 spaces for Javascript
* 4 spaces for the rest

Set your editor to replace your tabs with spaces before you're saving it in a Garp project.
An example for why we use spaces instead of tabs is for instance this docblock:

```php
/**
 * @param array $data      Content of new record
 * @param bool  $overwrite Wether to overwrite existing records.
 */
```

Will be all screwed up if aligned using tabs. Different contexts render tabs differently, so do not rely on them.

Same goes for aligning things like variable declarations:

```php
$blue   = 'Leonardo';
$red    = 'Rafael';
$orange = 'Michaelangelo';
$purple = 'Donatello';
```

Just use spaces. 
(a good Vim plugin to help with this is [Tabular](https://github.com/godlygeek/tabular))

## Line-length
Try to keep your lines readable. 
We stick to a line limit of 100 characters, but we do not enforce it - in case of urls and such.

Use [Husky](https://www.npmjs.com/package/husky) for hooks whenever possible.

In Vim you can enforce this/make this more visible using:

```
set textwidth=100
set colorcolumn=+1
```

## Private methods

Prefix private or protected methods and properties with an underscore:

```php
protected $_data = array();

protected function _doSomething() {
}
```

Also, generally we prefer `protected` over `private`.

## Function arguments

Try not to use more than three (3) arguments in a function or method definition:

```php
// BAD
function fetchFiltered($page, $limit, $color, $brand, $group, $something, $else, $etc) {
    // ...
}
```

Convert to a parameter object (or array):

```php
// GOOD
function fetchFiltered(array $params) {
    $page = $params['page'];
    // et cetera
}
```

See also ["Introduce Parameter Object"](http://sourcemaking.com/refactoring/introduce-parameter-object).

## Argument line distribution

When calling function and spreading arguments over multiple lines, you should place _all_ arguments on a separate line, not some. 
Especially not when the first argument is a closure or array and the second a wee scalar value.

```php
// NOT LIKE THIS:
doTheThing(function ($foo) {
  return $this->manipulateTheVar($foo);
}, $poo);

// LIKE THIS:
doTheThing(
  function ($foo) {
    return $this->manipulateTheVar($foo);
  },
  $poo
);
```

## Arrays
Try to write array/object/dictionary properties on new lines:

```php
$abc = [
    'pork' => 'butts',
    'beef' => 'cake',
    'fruit' => 'loop'
];
```

## Switch
If you have to use a `switch`, align the `case` and `break` statements:

```php
switch ($breakfast) {
    case 'cereal':
        // ...
    break;
    case 'bacon':
        // ...
    break;
    default:
        // ...
    break;
}
```
Also, remember that cool kids use [Replace Conditional With Polymorphism](http://refactoring.com/catalog/replaceConditionalWithPolymorphism.html).

## Curlies

Even though it's allowed to omit curly braces for one-liners, we generally prefer not to use them.

```php
if (!array_key_exists('Leonardo', $ninjaTurtles)) array_push($ninjaTurtles, 'Leonardo');
```

The reasoning for this is that it's easier to add lines in the future:

```php
if (!array_key_exists('Leonardo', $ninjaTurtles)) {
    array_push($ninjaTurtles, 'Leonardo');
    debug('Leonardo missing');
}
```

## Names

We prefer `CamelCase` for identifiers, but in Ruby you are allowed to use the underscored notation.

## Don't abbreviate

In general, prefer longer variable and method names over short variable names. 
This is not algebra, we're not trying to find `x`. 

Usually there's a proper semantic name for whatever value you're working with.

## Comments

Comments are added with the best of intentions, obviously. But take a moment to consider wether you can make the code self-explanatory before adding a comment. Maybe your variable names are ambiguous? Maybe your function parameters can be made a little bit more clear?

If you absolutely must, write a clear comment, use proper capitalisation and punctuation. We're not bloody animals.

## Functional programming

No hard and fast rules here, but try to stick to the following:

- Prefer maps/filters/reductions over looping
- Prefer immutable data

## Object Calisthenics

At Grrr, we practice [Object Calisthenics](http://williamdurand.fr/2013/06/03/object-calisthenics/).
Generally, the most important bits are:

### Only one level of indentation per method

Most commonly violated in loops:

```php
public function listTurtles() {
    foreach ($turtles as $turtle) {
        if ($turtle->hasWeapons()) {
            foreach ($turtle->getWeapons() as $weapon) {
                echo "{$turtle->name} uses weapon: {$weapon->name}";
            }
        }
    }
}
```

Can be fixed using something like:

```php
public function listTurtles() {
    $output = '';
    foreach ($turtles as $turtle) {
        $output .= $this->getOutput($turtle);
    }
    echo $output;
}

public function getOutputForTurtle($turtle) {
    if (!$turtle->hasWeapons()) {
        return;
    }
    $output = '';
    foreach ($turtle->getWeapons() as $weapon) {
        $output .= "{$turtle->name} uses weapon: {$weapon->name}";
    }
    return $output;
}
```

### Don't use the ELSE keyword
Don't use this:
```php
function getBreakfast() {
    if ($this->guest->isVegetarian()) {
        $bowl = new CerealBowl();
        $bowl->add($nuts);
        $bowl->add($berries);
        $bowl->add($milk);
        $breakfast = $bowl;
    } else {
        $breakfast = new Bacon();
    }
    return $breakfast;
}
```

Use an *early return*:

```php
function getBreakfast() {
    if (!$this->guest->isVegetarian()) {
        return new Bacon();
    }
    $bowl = new CerealBowl();
    $bowl->add($nuts);
    $bowl->add($berries);
    $bowl->add($milk);
    return $bowl;
}
```

