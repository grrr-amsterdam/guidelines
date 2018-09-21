# HTML/CSS Coding Standards
Authors: [Michèle van den Aardweg](https://twitter.com/MicheleNL), [Koen Schaft](https://twitter.com/KoenSchaft), [Mattijs Bliek](https://twitter.com/mattijsbliek)


### Writing style

- Use [SCSS](https://sass-lang.com/documentation/file.SASS_REFERENCE.html) (`.scss`) syntax, not the older Sass (`.sass`) syntax.
- Use 4 spaces to indent stuff.
- Write single line CSS, but keep [property grouping and order](#property-order--grouping) in mind.
- Limit your lines to 80 characters. If you need to split up lines, bundle related properties.
- Each selector's on its own line.
- Media queries get their own line.
- Values are preceded by a space.
- Selectors are followed by a space.
- Selectors, sass variables and mixins should be _kebab-case_ (`.site-header`, `$primary-color`, `@mixin bp-min`).
- Use [BEM](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/) writing style.
- Shortest name possible, while still being descriptive. So not `.picture`, but `.profile-picture` or `.user-profile-picture`.
- No abbreviations. Don't shorten `image` to `img` since there is no clear rule on common abbreviations.
- Create (scoped) variables for recurring property values througout your module or application (`$border-radius; 2px;`, `$card-padding-small: 40px;`).
- Avoid using 'magic numbers' or unnecessary complex solutions, when simpler solutions are available.

### Property order & grouping

While not enforced by linting, finding properties is easier when they're always in roughly the same order.

- `Visibility (visibility, opacity, overflow)`
- `Positioning (z-index, position, top/right/bottom/left, float etc.)`
- `Box model (box-sizing, margin, display, flexbox, width/height, padding, borders etc.)`
- `Colors (background, color)`
- `Fonts (font-family, font-weight, line-height, text-decoration etc.)`
- `Transitions and transforms`
- `Various (box-shadow etc.)`

We deem certain properties more important than others. When a property influences other elements, it's probably more imporant than a property which does not. Also keep in mind how the [box model](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Introduction_to_the_CSS_box_model) roughly works. With our property order, we're mostly going from the outside to the inside, following the visual representation of this model.

A common example would be:

```css
/* good */
.class {
    z-index: 100; position: absolute; top: 0; left: 0;
    display: block; width: 100%; height: 100%;
    background: $blue; color: $white;
}
```

If we would just append properties on the fly, we would always be looking for them, resulting in sloppy code and possible regressions since you might miss important properties:

```css
/* bad */
.class {
    width: 100%; height: 100%;
    background: $blue;
    color: $white;
    position: absolute; top: 0;
    display: block; z-index: 100; left: 0;
}
```

By grouping and ordering properties in a consistent manner, we can see at a glance what the element will look like and how it's positioned. This reduces a lot of cognitive load.


### Utility classes

Avoid using utility classes, especially for overall styling. It will inevitably lead to unforseen bugs and unnecessary complexity.

- It creates unnecessary cognitive load, since you might have to combine the properties of 5 classes in your head to get the complete 'selector'.
- Utility classes are usually combined, resulting in specificity issues. The order of the appended class does not matter, only the cascade in our stylesheet dictates which property 'wins'.
- Some developers might be more familiar with them than others. This easily leads to bugs, since changing the styling for element A, also changes the styling for element B, F and Y.
- You might want to have a small variation since element C needs to look slightly different than element B, leading to possibly many variations of a simple utility class.
- It creates the false illusion that a (back-end) developer can create the styling for a newly created feature by glueing utility classes together. This sometimes works. It sometimes simply does not.

Can you tell what the following element will look like?

```html
<div class="text block widget widget--small margin-top left"></div>
```

You might have a general idea. But you still would need to look up all those classes, and combine them in your head to get the full picture. Furthermore, changing `.text` for this specific element, might break another element using that same `.text`.

```html
<div class="scoped-module"></div>
```

Using a specific module class for our element, means that all our styling is scoped to that module. This means that we can look up `.scoped-module` and be sure that everything is in there. Changing that module will only change (and possibly break) that module.

Some things to keep in mind:

- Using scoped modules means that we sometimes might have duplicate groups of properties, which are shared between multiple modules. This however, does not wheigh up to the fact that it's a lot safer and less complex to work with scoped modules.
- To combat 'manual duplication', you can always write a simple mixin and import that in your module. Beware that this can lead to generic 'utility'-mixins as well, and could shift parts of the problem elsewhere when used incorrectly.


### ID selectors

Never use IDs in CSS. Ever. They have no advantage over classes (anything you can do with an ID, you can do with a class), they cannot be reused, and their specificity is way, way too high. Even an infinite number of chained classes will not trump the specificity of one ID.
Note: this does not mean that we don't use IDs in our markup, since they are used for accessibility purposes (eg. `aria-controlledby="the-other-id"`) or linkable anchors (eg. `href="#section-1"`).


### Tag selectors

Use tag selectors sparingly. Only use them for elements that are non-generic, and have a fixed DOM structure or can be the only one in your module. Some examples: `ul`, `li`, `dl`, `dt`, `dd`, `details`, `summary`, `figure`, `img`, `h1`.

```html
/* bad */
<nav class="site-nav">
    <div>
        <ul class="site-nav__list">
            <li><a href="#"></li>
            <li><a href="#"></li>
            <li><a href="#"></li>
        </ul>
    </div>
</nav>

/* good */
<nav class="site-nav">
    <ul>
        <li><a href="#"></li>
        <li><a href="#"></li>
        <li><a href="#"></li>
    </ul>
</nav>

/* also good */
<nav class="site-nav">
    <ul class="site-nav__list">
        <li class="site-nav__item"><a href="#"></li>
        <li class="site-nav__item"><a href="#"></li>
        <li class="site-nav__item"><a href="#"></li>
    </ul>
</nav>
```

When in doubt, use classes and try to be consistent.


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
  <section class="glass">
      <div class="glass__inner">
          ...
      </div>
  </section>
```


### Sass @extend

Using the extend function in Sass, PostCSS or other tools quickly leads to bloated code and complex modules. It also makes it tricky to determine where styles come from. Use the good old comma, or a mixin where appropriate, if selectors need the same styles.


### Specificity and order

**Do not rely on import order**, modules will be imported alphabetically.

Do not qualify selectors unless you have a compelling reason to do so. If `.nav {}` will work, do not use `ul.nav {};`. To do so would limit the places you can use the .nav class and increase the specificity of the selector.


### JavaScript hooks

Never couple JavaScript and CSS. If you need a class as an anchor for your javascript, prepend it with `.js-` or use a data attribute.

```html
<a href="img.jpg" class="js-toggle-lightbox">View large image</a>
<a href="img.jpg" data-handler="lightbox">View large image</a>

<div class="lightbox js-lightbox"></div>
<div class="lightbox" data-enhancer="lightbox"></div>
````


### Modularity

If you have a module called `.event`, there should be a corresponding `event.css` (or Sass equivalent) file. Preferably, there should also be an HTML file to match.

In the file `event.css`, all selectors should start with `.event`, eg. `.event__body` or `.event h1`. Using other module selectors makes the modules dependant on each other and no longer modular.

The one exception to this rule is selectors for global [state classes](#states), which can be used throughout your application.

```css
/* bad */
.block-list {
  ...
}

.block-list .event {
  margin-bottom: 30px;
}

/* good */
.block-list {
  ...
}

.block-list__event { /* this is a container for `.event` */
  margin-bottom: 30px;
}

/* good */
.block-list {
  ...
}

.block-list > li + li { /* this is a container for `.event` */
    margin-top: 30px;
}
```

```html
<!-- bad -->
<section class="block-list">
    <div class="block-list__event event"></div>
</section>

<!-- good -->
<section class="block-list">
    <div class="block-list__event">
         <!-- @import the module -->
    </div>
</section>

<!-- good -->
<ul class="block-list">
    <li>
         <!-- @import the .event module -->
    </li>
    <li>
         <!-- @import the .event module -->
    </li>
</ul>
```

If you find that there is a part of a module which can be independently re-used, refactor it to its own module. The more styles put together in a module, the harder it becomes to maintain.

When in doubt, split up the module.

### States

Your element might have states, such as 'active', or 'hidden'. State classes always start with `is-...` or `has-...` and do not contain the module parents' name. Furthermore, there can be global state classes, usually appended to the `html` or `body` elements.

In some cases a state attribute might be more approprite, for example: `[data-direction="top/right/bottom/left"]`.

Prefer ARIA attributes whenever possible. As they provide styling hooks while improving the accessibility at the same time (e.g. `[aria-expanded="true"]`, `[aria-hidden="true"]`).


### Documentation

If you have to write code that might seem odd or out of place to other developers, always provide a comment to explain why that code is there.

Some examples of where comments are a necessity:

- When using magic numbers (in fact, try to always use Sass variables instead of magic numbers. It makes them easier to reason about, re-use and use in calculations)
- When qualifying elements
- When using `!important`
- When increasing specificity by doubling a selector
- When stripping whitespace because you're using inline block
- When writing workarounds for specific browsers


## Front-end guidelines

### Assets

- Only use open source fonts or fonts with an obtained license
- Preload webfonts and asynchronous JavaScript
- Don't load font weights or variants that won't be used
- Use our [SVG helper](https://github.com/grrr-amsterdam/garp_scaffold/blob/master/application/modules/default/views/helpers/Svg.php) for including SVGs
- Don't use icon fonts

### Performance

- Lazy load media embeds from Youtube, Vimeo, SoundCloud etc.
- Use responsive images
- Use cheap CSS animations or transitions (transforms, opacity)
- Throttle or debounce expensive listeners

### Accessibility

Web accessibility is important to us. Please comply to at least [WCAG2.0](https://www.w3.org/TR/WCAG20/) level A
