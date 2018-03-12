# HTML/CSS Coding Standards
Author: Mattijs Bliek


### Writing style

- Use 4 spaces to indent stuff
- Write single line CSS
- Limit your lines to 80 characters. If you need to split up lines, bundle related properties.
- Each selector's on its own line.
- Media queries get their own line.
- Values are preceded by a space.
- Selectors are followed by a space.
- Selectors should be lowercase
- Sass variables should be camelCase
- Use [BEM](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/) writing style.
- Shortest name possible, while still being descriptive. So not `.picture`, but `.profile-picture` or `.user-profile-picture`.
- No abbreviations. Don't shorten 'image' to 'img' since there is no clear rule on common abbreviations.


### Property order

While not enforced by linting, finding properties is easier when they're always in roughly the same order.

- `Visibility (visibility, opacity, overflow)`
- `Positioning (z-index, position, float etc.)`
- `Box model (box-sizing, display, flexbox, width/height, margin, padding, borders etc.)`
- `Colors (background, color)`
- `Fonts (font-family, line-height, text-decoration etc.)`
- `Transitions and transforms`
- `Various (box-shadow etc.)`


### IDs in CSS

Never use IDs in CSS. Ever. They have no advantage over classes (anything you can do with an ID, you can do with a class), they cannot be reused, and their specificity is way, way too high. Even an infinite number of chained classes will not trump the specificity of one ID.


### Nesting

Do not nest selectors unnecessarily. This increases both complexity and specificity.

```css
/* bad */
.element div h3 {
  ...
}

/* good */
.element h3 {
  ...
}

/* also good */
.element__subheading {
  ...
}
```

Also never nest elements within each other when using Sass. This leads to code that is harder to read and less maintainable.

```css
/* bad */
.selector {
  margin: 10px; padding: 10px;
  h3 {
    text-transform: uppercase;
  }
}

/* good */
.selector {
  margin: 10px; padding: 10px;
}

.selector h3 {
  text-transform: uppercase;
}
```

Nesting modules in HTML is allowed and even encouraged.

```html
  <div class="glass">
    <div class="glass__contents beer">
    ...
    </div>
  </div>
```


### Sass @extend

Using the extend function in Sass, PostCSS or other tools quickly leads to bloated code and complex modules. It also makes it tricky to determine where styles come from. Use the good old comma, or a mixin where appropriate, if selectors need the same styles.


### Specificity and order

**Do not rely on import order**, modules will be imported alphabetically.

Do not qualify selectors unless you have a compelling reason to do so. If `.nav {}` will work, do not use `ul.nav {};`. To do so would limit the places you can use the .nav class and increase the specificity of the selector.

If you have to increase specificity, double the class name. For example: `.button.button` is twice as specific as `.button`. When resorting to this, document why it's necessary.


### JavaScript hooks

Never couple JavaScript and CSS. If you need a class as an anchor for your javascript, prepend it with `.js` or use a data attribute.

```html
<a href="img.jpg" class="js-toggle-lightbox">View large image</a>

<a href="img.jpg" data-handler="lightbox">View large image</a>
````


### Modularity

If you have a module called `.event`, there should be a corresponding `event.css` (or Sass equivalent) file. Preferably, there should also be an HTML file to match.

In the file `event.css`, all selectors should start with `.event`. Using other module selectors makes the modules dependant on each other and no longer modular.

The one exception to this rule is selectors for [state classes](#states), which can be used throughout your application.

```css
/* bad */
.block-list { 
  padding: 20px; background: #ddd; 
}

.block-list .event { 
  margin-bottom: 30px; 
}
```

```css
/* good */
.block-list { 
  padding: 20px; background: #ddd; 
}

.block-list__event { 
  margin-bottom: 30px; 
}
```


If you find that there is a part of a module which can be independently re-used, refactor it to its own module. The more styles put together in a module, the harder to maintain it becomes.

When in doubt, split up the module.


### States

Your element might have states, such as 'active', or 'hidden'. State classes always start with `is-...` or `has-...` and do not contain the module parents' name.

Prefer ARIA attributes whenever possible. As they provide a styling hooks while improving the accessibility at the same time (e.g. `aria-expanded="true"`).


### Documentation

If you have to write code that might seem odd or out of place to other developers, always provide a comment to explain why that code is there.

Some examples of where comments are a necessity:
- When using magic numbers (in fact, try to always use Sass variables instead of magic numbers. It makes them easier to reason about, re-use and use in calculations)
- When qualifying elements
- When using `!important`
- When increasing specificity by doubling a selector
- When stripping whitespace because you're using inline block
- When writing workarounds for specific browsers