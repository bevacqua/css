# .bc-style-guide {

> The so-called `"nico-style"` brought to markdown. **Because Style Guide**.

This style guide is a jab at solving collisions between CSS class names, and issues that ultimately lead to confusion, having to use `!important` rules, copying and pasting style declarations, and similarly awful aspects of CSS development.

## Goal

These suggestions aren't set in stone, they aim to provide a baseline you can use in order to write more consistent codebases. To maximize effectiveness, share the styleguide among your co-workers and attempt to enforce it. Don't become obsessed about code style, as it'd be fruitless and counterproductive. Try and find the sweet spot that makes everyone in the team comfortable developing for your codebase, while not feeling frustrated that their code always fails automated style checking because they added a single space where they weren't supposed to. It's a thin line, but since it's a very personal line I'll leave it to you to do the drawing.

> Astonishingly, this style guide won't do anything for you that you're not able to figure out for yourself. That means you won't see the benefits unless you try and follow most of the conventions laid out next.

> Use together with [bevacqua/js][6] for great good!

Feel free to fork this style guide, or better yet, send [Pull Requests][3] this way!

## Table of Contents

1. [Namespaces](#namespaces)
1. [Classes](#classes)
1. [Attributes](#attributes)
1. [`id` attribute](#id-attribute)
1. [Tag Names](#tag-names)
1. [Selectors and Nesting](#selectors-and-nesting)
1. [Organization](#organization)
1. [Presentation-Specific vs Layout-Specific Styles](#presentation-specific-vs-layout-specific-styles)
1. [Styles](#styles)
1. [Media Queries](#media-queries)
1. [Frameworks and Vendor Styles](#frameworks-and-vendor-styles)
1. [Languages](#languages)
  - [Not Stylus](#not-stylus)
  - [Not CSS](#not-css)
  - [Stylus](#stylus)
1. [License](#license)

## Namespaces

Components should _always_ be assigned a unique namespace prefix.

- The namespace can be as short as a single character, or as long as 5 characters
- Where possible, the namespace should be a meaningful shorthand
- In class names, the namespace must be followed by a single dash
- Views should be treated as individual components

Consider the following example, where we assigned `ddl` to a **drop down list** component. Take note of the class names.

##### Good

```css
.ddl-container {
  // ...
}

.ddl-item-list {
  // ...
}

.ddl-item {
  // ...
}
```

##### Bad

```css
.item-list {
  // ...
}

.dropdown-item-list {
  // ...
}

.xyz-item-list {
  // ...
}
```

Classes that are meant to be shared among a large set of elements, or provide reusable styles, should be grouped under a universal namespace, such as `uv`.

##### Good

```css
.uv-clearfix {
  // ...
}
```

##### Bad

```css
.clearfix {
  // ...
}
```

See [Selectors and Nesting](#selectors-and-nesting) for information in regard to how styles should be overridden

## Classes

Class names must follow a few rules.

- Must be all-lowercase
- Words must be separated by single dashes
- As short as possible, but as long as necessary
  - _Don't abbreviate words carelessly_
- Name things consistently
- Meaningful description of the elements that should use it
- Keep your non-prefix word count below 4

##### Good

```css
.ddl-item {
  // ...
}

.ddl-selected {
  // ...
}

.ddl-item-selected {
  // ...
}
```

##### Bad

```css
.ddlItem {
  // ...
}

.ddl-item-container-text {
  // ...
}

.ddl-foo-bar-baz {
  // ...
}
```

## Attributes

Attributes make decent selectors from time to time. Some ground rules apply.

- If the "exists" check suffices, use that
- Don't overqualify using a tag name

##### Good

```css
[href] {
  // ...
}
```

##### Bad

```css
a[href] {
  // ...
}

[href^='http'] {
  // ...
}
```

## `id` attribute

While the `id` attribute might be fine in HTML and JavaScript, it should be **avoided entirely** inside stylesheets. Few reasons.

- ID selectors are not reusable
- Priority nightmares
- [“Bad Code”, Dogmatism, etc.][1]

##### Good

```css
.ur-name {
  // ...
}
```

##### Bad

```css
#ur-name {
  // ...
}
```

Just assign a class name to the element.

## Tag Names

Tag names in selectors follow a few rules.

- Application level styles that are only overridden in a few places are okay to use tag name selectors
- [Not semantic][2]. **Avoid where possible**, use class names instead
- Fine to use when there's a ton of elements under the same namespace that need a small tweak
- **Don't overqualify** _(`a.foo`)_

##### Good

```css
button {
  padding: 5px;
  margin-right: 3px;
}

.ddl-button {
  background-color: #f00;
}
```

##### Bad

```css
.ddl-container button {
  background-color: #f00;
}
```

## Selectors and Nesting

Styles shouldn't need to be nested more than three _(four at worst)_ levels deep. This includes pseudo-selectors. If you find yourself going further, think about re-organizing your rules _(either the specificity needed, or the layout of the nesting)_.

##### Good

```css
.sg-title-icon:before {
  // ...
}

.dg-container .sg-title {
  font-size: 1.1em; // larger segment title inside dialogs
}
```

##### Bad

```css
.dg-container .sg-container .sg-title {
  font-size: 1.1em;
}

.dg-container .sg-title span:before {
  // ...
}
```

If a component needs to be different within another component, these rules apply.

- Where possible, give a class name using the parent namespace to the child component
- If that's not possible, then use a nested selector

Suppose you have a User List component `.ul-*` and a User Card component `.uc-*`.

##### Good

```html
<div class='ul-container'>
  <div class='uc-container'>
    <span class='uc-name ul-card-name'>John Doe</span>
  </div>
</div>
```

```css
.ul-card-name {
  // ...
}
```

##### Okay

```html
<div class='ul-container'>
  <div class='uc-container'>
    <span class='uc-name'>John Doe</span>
  </div>
</div>
```

```css
.ul-container .uc-name {
  // ...
}
```

##### Bad

```html
<div class='ul-container'>
  <div class='uc-container'>
    <span class='uc-name uc-name-in-ul'>John Doe</span>
  </div>
</div>
```

```css
.uc-name-in-ul {
  // ...
}
```

## Organization

Ideally, you should keep your stylesheets separated in different files. Either of the approaches below is fine. The former is prefered.

- Use a single `all.{styl,less,scss}` file and have it `@import` every other file
- Use a build tool to glob the styles directory

A few rules apply.

- Each component should take up its own file
- Styles applied globally to tag names, see [Tag Names](#tag-names), should be kept in a single file
- Where possible split **presentation-specific** styles from **layout-specific** styles. See below

## Presentation-Specific vs Layout-Specific Styles

Presentation-Specific styles are those that **only alter the visual design** of the element, but don't change its dimensions or position in a meaningful way. The examples below are presentation-specific.

- Rules such as `color`, `font-weight`, or `font-variant`
- Rules that animate other properties
- `font-size` is not considered a meaningful dimension change
- `padding` may fit this category _(loosely)_, but only if `box-sizing: border-box;` is in effect
- `max-width` and `max-height` may fit either category, but it's generally reasonable to consider them presentation-specific

Layout-Specific Styles are those that change the dimensions or positioning of DOM elements. These are mostly layout-specific.

- Rules such as `margin` or `padding`
- `width`, and `height`
- The element's `position`
- `z-index`, definitely

Where possible, it's suggested to explicitly split styles into these two categories. The explicit differentiation could be made in a few different ways.

- _(bad)_ No differentiation
- _(decent)_ Layout-specific first, presentation-specific later
- _(good)_ A line-break between both categories
- _(better)_ Split in subsequent style declarations using the same selector
- _(best)_ Declaring the rules in different files altogether

##### Good

```css
.foo {
  position: fixed;
  top: 8px;
  right: 8px;
  padding: 2px;
  font-weight: bold;
  background-color: #333;
  color: #f00;
}
```

```css
.foo {
  position: fixed;
  top: 8px;
  right: 8px;
  padding: 2px;

  font-weight: bold;
  background-color: #333;
  color: #f00;
}
```

```css
.foo {
  position: fixed;
  top: 8px;
  right: 8px;
  padding: 2px;
}

.foo {
  font-weight: bold;
  background-color: #333;
  color: #f00;
}
```

##### Bad

```css
.foo {
  font-weight: bold;
  background-color: #333;
  color: #f00;
  position: fixed;
  top: 8px;
  right: 8px;
  padding: 2px;
}

.foo {
  right: 8px;
  color: #f00;
  padding: 2px;
  top: 8px;
  background-color: #333;
  font-weight: bold;
  position: fixed;
}
```

## Styles

These rules apply to your CSS property values

- If the value of a property is `0`, do not specify units
- The `!important` rule should be aggressively avoided.
  - Keep style rules in a sensible order
  - Compose styles to dissipate the need for an `!important` rule
  - Fine to use in limited cases
    - Overlays
    - Declarations of the `display: none !important;` type
- Keep `z-index` levels in variables in a single file. **Avoids confusion** about what level should be given to an element, and arbitrarily-high `999`-style values
- Use hex color codes `#000` unless there's an explicit need for an `rgba` declaration
- Dislike magic numbers
- Avoid mixing units
- Unit-less `line-height` is preferred because it does not inherit a percentage value of its parent element, but instead is based on a multiplier of the `font-size`.

##### Good

```css
.btn {
  color: #222;
}

.btn-error {
  color: #f00;
}
```

##### Bad

```css
.btn-red {
  color: #f00 !important;
}

.btn {
  color: #222;
}
```

## Media Queries

> If you are reading this, I salute you. You're _almost as boring_ as I am. I'm more boring because **I actually wrote the damn thing**. It's not a contest, though.

A few rules apply to media queries.

- Settle for a few _(2-3)_ breakpoints and use those only
- Don't wrap entire stylesheets in media queries
- Instead, modularize media queries wherever possible, keep them relevant to the components
- Approach your styles in a [Mobile First][5] manner. Generally you add more things as you get more real estate. **Mobile First** logically follows

##### Good

```css
.co-field {
  width: 120px;
}

@media only screen and (min-width: 768px) {
  .co-field {
    width: 400px;
    color: #f00;
  }
}
```

##### Bad

```css
.co-field {
  width: 400px;
  color: #f00;
}

@media only screen and (max-width: 768px) {
  .co-field {
    width: 120px;
    color: initial;
  }
}
```

## Frameworks and Vendor Styles

You should shy away from all of these. A few rules apply.

- **Stay away from frameworks**
- Use [`normalize.css`][4] if you want
- Vendor styles, such as those required by external components are okay, and they should come before you define any of your own styles

## Languages

Some rules apply to stylesheet, regardless of language.

- Use a pre-processor language where possible
- Use soft-tabs with a two space indent
- One line per selector
- One _(or more)_ line(s) per property declaration
  - Long, comma-separated property values _(such as collections of gradients or shadows)_ can be arranged across multiple lines in an effort to improve readability and produce more useful diffs.
- Comments that refer to selector blocks should be on a separate line immediately before the block to which they refer
- Use a plugin such as **TrailingSpaces** in Sublime Text to get rid of trailing spaces

##### Good

```css
.foo {
  color: #f00;
}

.foo,
.bar,
.baz {
  color: #f00;
}
```

##### Bad

```css
.foo {
    color: #f00;
}

.foo, .bar, .baz {
  color: #f00;
}

.foo {
  color: red;
}
```

### Not Stylus

These rules apply to every language except Stylus.

- Always end property declarations with a semicolon
- Put a single space after `:` in property declarations
- Put spaces before `{` in rule declarations

##### Good

```css
.foo {
  color: #f00;
}
```

##### Bad

```css
.foo{
  color: #f00;
}

.foo {
  color:#f00;
}

.foo {
  color: #f00
}
```

### Not CSS

Rules applicable to most pre-processor languages.

- Put comments in `//` statements
- Prefer nested selectors `.foo { .bar {} }` vs `.foo .bar {}`
  - _Only if both `.foo` and `.foo .bar` need styling_
- Use `&{selector}` to concatenate selectors
- Don't blindly over-nest
- **Keep `z-index` levels in a single file, using variables**

##### Good

```less
// foo
```

```less
.bar {
  // ...

  .baz {
    ///...
  }
}
```

##### Bad

```less
/* foo */
```

```less
.bar {
  // ...
}

.bar .baz {
  // ...
}
```

### Stylus

Rules specific to Stylus.

- Omit brackets `{}` in rule declarations
- Omit `:` and `;` in property declarations
- Use transparent mix-ins
- Use `nib`

##### Good _(Stylus)_

```stylus
// foo
```

```stylus
.foo
  color #f00
```

```stylus
.foo
  color #f00

  .bar
    padding 2px
```

##### Bad _(Stylus)_

```stylus
/* foo */
```

```stylus
.foo {
  color: #f00;
}
```

```stylus
.foo {
  color: #f00;
}

.foo .bar {
  padding: 2px;
}
```

## License

MIT

> Fork away!

# }

[1]: http://css-tricks.com/bad-code-dogmatism-etc/ "Bad Code, Dogmatism, etc"
[2]: http://smacss.com/ "SMACSS modular CSS architecture"
[3]: https://github.com/bevacqua/css/issues "bevacqua/css Issues on GitHub"
[4]: http://necolas.github.io/normalize.css/ "A modern, HTML5-ready alternative to CSS resets"
[5]: http://bradfrostweb.com/blog/mobile/the-many-faces-of-mobile-first/ "The Many Faces of Mobile First"
[6]: https://github.com/bevacqua/js
