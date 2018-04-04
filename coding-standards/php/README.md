# PHP coding standards

## General

### Strings
Don't use double-quoted strings unless you're actually embedding variables in them. Single-quoted strings won't be parsed for variables and will therefore be better for performance.

```
<?php
// Good
$foo = "Hello {$username}, such nice weather today."; 

// Good
$foo = 'Hi there, such nice weather today.';

// Bad
$foo = "Hi there, such nice weather today.";

// Good, but we prefer example 1:
$foo = 'Hello ' . $username . ', such nice weather today.';
?>
```

## Class member order

Place class members in the following order:

- Any used traits
- class constants
- public class properties
- private/protected class properties
- class constructor
- public methods
- private/protected methods

## Zend Framework Controller actions
In order to keep it simple (stupid), please refrain from using anything other than lowercase alphanumeric characters in your controller actions. 
So use thankyouAction instead of thankYouAction.
First of all, if you use uppercase characters inflection will be applied, and you have to think about how to name your views (thank-you.phtml in this case), which is a minor nuisance.

More importantly however, Zend's static caching fails mysteriously when the action contains uppercase characters. I don't know why, I don't even really want to know why. It is very lame, but using all-lowercase action names is no biggie, all things considered.

## `self` versus `static`
It is highly recommended to not use the `self` keyword. This can break inheritance in unexpected ways.
Consider the following example:
```
<?php
class A {
	static function output() {
		echo 'hello, this is A';
	}

	public function doSomething() {
		self::output();
	}
}

class B extends A {
	static function output() {
		echo 'hello, this is B';
	}
}

$b = new B();
$b->doSomething();
?>
```

The output of that script is "hello, this is B", instead of "hello, this is A", which you'd maybe expect. 
The culprit in this case is the self keyword. self refers to the class in which it is defined. In order to change the above example to match your expectations you should either copy the entire method to class B, since the mention of self in class B would point it to B.

But copying code should always be a last resort. PHP 5.3 gives us a new, better keyword to solve this problem: static.
static refers to the context at the moment of execution. The following example works, because static resolves to B instead of A:

```
<?php
class A {
	static function output() {
		echo 'hello, this is A';
	}

	public function doSomething() {
		static::output();
	}
}

class B extends A {
	static function output() {
		echo 'hello, this is B';
	}
}

$b = new B();
$b->doSomething();
?>
```

It is therefore recommended to prefer static over self, since it allows future subclassing.
