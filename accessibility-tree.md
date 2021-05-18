# Accessibility Tree

**TL;DR**
- The browser takes the DOM tree and modifies it into a tree that is useful for assistive technologies
- The modified tree is the _Accessibility Tree_
- the accessibility tree is used by platform-specific Accessibility APIs as an interface for assistive technologies (like screen readers)

## The Accessibility Tree object

There are four things in an accessibility tree object

### Name
- How do we refer to the thing?
- The name dependes on the element. 
- Most elements use the `textContent` as the name, but some elements use 
- Example:
  - `<a href="www.useful-website.com">Read more</a>`
  - A link with the text "Read more" with have a `name` of `Read more`
- Not all semantic native HTML elements provide an accessible name by default.
- Generally, there are three types of `name`s: 
  - **`textcontent`**
    - for most text elements, the name is their `textContent`
  - **Visible labels**:
    - used by all users to associate meaning with an element
    - Example: a label associated with an input
  - **Text alternatives**:
    - used when there is no need for a visual label
    - Example: when an image has no `alt` text, its name is derived from the `src` attribute. When the `alt` attribute contains text, it is used as the `name` of the `img` in the accessibility tree

### Description
- How do we describe the thing?
- This is often taken from the `title` attribute or the text of an element referred to by the `aria-describedby` attribute
- Example:
  -  A description associated with a `table` element provides contextual information on what data the table contains

### Role
- What kind of thing is it?
- This can be either a default role provided by the browser, or a role given to it via a WAI-ARIA role attribute
- Example: 
  - Buttons, by default, have a role of `button`. 
  - But you can also override this role with the following ARIA roles:`checkbox`, `link`, `menuitem`, `menuitemcheckbox`, `menuitemradio`, `option`, `radio`, `switch`, `tab`. 

### Value
- What is the value of the element? 
- This can mean different things depending on the element
- Example: 
  - a form `input` would have a value of whatever is entered in the input
  - a link's value would be the `url` in the element's `href` attribute

### State
- Does it have a state?
- Example: 
  - checkboxes include a `checked` or `unchecked` state by deafult. 
  - The `<summary>` element has a `collapsed` and `expanded` state by default.

### Actions
- What actions can be performed on the item
- Example: 
  - a `<button>` would have "press" listed under `actions:[]`
  - a link would have "jump" listed under `actions:[]`

Additional info...

 ### DOMNode
 - What DOM node does this item in the accessibility tree refer to?
 - Clicking on this in the accessibility tree will take you to the DOM node in the page inspector

### keyboardShortcut
- What keyboard shortcuts are available to activate the element? 
- This is specified in an elements `accesskey` attribute

### childCount
- number of child items the current item has 

### indexInParent
- an index value indicating what number the child item is inside its parent (starting with 0)

### relations
- a list of accessibility- relevant relationships
- Example:
  -  an `input` associated with a `label` will have a `labelled by` relation

### attributes
- a list of accessibility- relevant attributes on an item
- Example:
  - style-related attributes like text-indent
  - useful states like "draggable" or "heading level"

## Getting implicit accessibility for free
- many of the native HTML elements have _implicit_ semantic meaning
  - these elements are recognized by all browsers and behave predictably
  - because they are established and stable, accessibility is often handled automatically for these elements
  - for example: the `<button>` element already has an inherent role, state, value, and expected keyboard behavior that the browser can use to build the accessibility tree

## Additional benefits of the accessibility tree
- Because accessibility APIs provide a convenient and universal way to explore and control applications, they're often used for automated testing scripts, and UI automation software like password managers.
 

## Resources
- Google Web Dev: https://developers.google.com/web/fundamentals/accessibility/semantics-builtin/the-accessibility-tree#:~:text=The%20accessibility%20tree%20is%20what,assistive%20technology%20via%20an%20API
- MDN: https://developer.mozilla.org/en-US/docs/Glossary/Accessibility_tree
- https://chromium.googlesource.com/chromium/src/+/HEAD/docs/accessibility/overview.md
