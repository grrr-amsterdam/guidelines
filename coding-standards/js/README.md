# Javascript

## Formatting your code

First things first: take a look at [the general coding standards](../general) to make yourself familiar with the recommended coding style.

## General flow of operations

Recently, we adopted the handlers/enhancers pattern by Hidde De Vries. [Read about handlers and enhancers here](https://hiddedevries.nl/en/blog/2015-04-03-progressive-enhancement-with-handlers-and-enhancers).

Generally, anything should be either an enhancer or handler. If you follow this pattern your colleagues never have to search far for your module.

Speaking of modules: recent versions of the scaffold use actual ES6 modules:

```
import FlashMessage from './modules/flashmessage.js';
```

Make sure your code is self-contained in a single file whose name reflects its module name.

## Prefer `const` over `let`

Immutable data is a very good idea. It provides confidence and ease of mind.  
This idea is not implemented completely when using `const`, unfortunately (`foo.bar = 123` – mutating an object is still possible, as is pushing to an array), but at least you can be sure the variable is not reassigned. 
Also, in general we feel it forces you to think about better solutions than iteratively mutating the same variable.

## ES6 

We use Ecmascript 6.
We encourage you to learn it, and use its shiny features. As mentioned, recent versions of the scaffold use ES6 modules which neatly organise our modules.

