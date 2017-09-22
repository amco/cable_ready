[![Lines of Code](http://img.shields.io/badge/lines_of_code-80-brightgreen.svg?style=flat)](http://blog.codinghorror.com/the-best-code-is-no-code-at-all/)
[![Code Status](http://img.shields.io/codeclimate/github/hopsoft/cable_ready.svg?style=flat)](https://codeclimate.com/github/hopsoft/cable_ready)
[![Dependency Status](http://img.shields.io/gemnasium/hopsoft/cable_ready.svg?style=flat)](https://gemnasium.com/hopsoft/cable_ready)

# CableReady

CableReady provides a standard interface for invoking common client-side DOM operations
from the server via [ActionCable](http://guides.rubyonrails.org/action_cable_overview.html).

Learn more about CableReady by reading through & running the [CableReady Test](https://github.com/hopsoft/cable_ready_test) project.

## Supported DOM Operations

_NOTE: It's possible to invoke multiple DOM operations with a single ActionCable broadcast._

### DOM Events

#### [dispatchEvent](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/dispatchEvent)

Dispatches a DOM event in the browser.

```ruby
cable_ready_broadcast "MyChannel", dispatch_event: [{
  event_name: "string", # required - the name of the DOM event to dispatch (can be custom)
  element_id: "string", # [window] - the DOM element id of the desired event target
  detail:     "object"  # [null]   - assigned to event.detail
}]
```

### Element Mutations

#### [innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML)

Sets the innerHTML of a DOM element.

```ruby
cable_ready_broadcast "MyChannel", inner_html: [{
  element_id: "string", # required - the DOM element id of the element to be mutated
  html:       "string"  # [null]   - the HTML to assign
}]
```

### [textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent)

Sets the text content of a DOM element.

```ruby
cable_ready_broadcast "MyChannel", text_content: [{
  element_id: "string", # required - the DOM element id of the element to be mutated
  text:       "string"  # [null]   - the text to assign
}]
```

### [insertAdjacentHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/insertAdjacentHTML)

Inserts HTML into the DOM relative to an element.
Supports behavior akin to prepend & append.

```ruby
cable_ready_broadcast "MyChannel", insert_adjacent_html: [{
  element_id: "string", # required    - the DOM element id of the referenced element
  position:   "string", # [beforeend] - the relative position to the DOM element (beforebegin, afterbegin, beforeend, afterend)
  html:       "string"  # [null]      - the HTML to insert
}]
```

### [insertAdjacentText](https://developer.mozilla.org/en-US/docs/Web/API/Element/insertAdjacentText)

Inserts text into the DOM relative to an element.
Supports behavior akin to prepend & append.

```ruby
cable_ready_broadcast "MyChannel", insert_adjacent_text: [{
  element_id: "string", # required    - the DOM element id of the referenced element
  position:   "string", # [beforeend] - the relative position to the DOM element (beforebegin, afterbegin, beforeend, afterend)
  text:       "string"  # [null]      - the text to insert
}]
```

### [remove](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/remove)

Removes an element from the DOM.

```ruby
cable_ready_broadcast "MyChannel", remove: [{
  element_id: "string" # required - the DOM element id of the element to be removed
}]
```

### [replace](https://developer.mozilla.org/en-US/docs/Web/API/Node/replaceChild)

Replaces a DOM element with new HTML.

```ruby
cable_ready_broadcast "MyChannel", replace: [{
  element_id: "string", # required - the DOM element id of the element to be replaced
  html:       "string"  # [null]   - the HTML to use as replacement
}]
```

### Attribute Mutations

#### [setAttribute](https://developer.mozilla.org/en-US/docs/Web/API/Element/setAttribute)

Sets an attribute on an element.

```ruby
cable_ready_broadcast "MyChannel", set_attribute: [{
  element_id: "string", # required - the DOM element id of the element to be mutated
  name:       "string", # required - the attribute to set
  value:      "string", # [null]   - the value to assign to the attribute
}]
```

#### [removeAttribute](https://developer.mozilla.org/en-US/docs/Web/API/Element/removeAttribute)

Removes an attribute from an element.

```ruby
cable_ready_broadcast "MyChannel", remove_attribute: [{
  element_id: "string", # required - the DOM element id of the element to be mutated
  name:       "string"  # required - the attribute to remove
}]
```
### CSS Class Mutations

#### [addCssClass](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList)

Adds a css class to an element.
This is a `noop` if the css class is already assigned.

```ruby
cable_ready_broadcast "MyChannel", add_css_class: [{
  element_id: "string", # required - the DOM element id of the element to be mutated
  name:       "string"  # [null]   - the CSS class to add
}]

```
#### [removeCssClass](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList)

Removes a css class from an element.

```ruby
cable_ready_broadcast "MyChannel", add_css_class: [{
  element_id: "string", # required - the DOM element id of the element to be mutated
  name:       "string"  # [null]   - the CSS class to remove
}]
```

### Dataset Mutations

#### [setDatasetProperty](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/dataset)

Sets an dataset property (data-* attribute) on an element.

```ruby
cable_ready_broadcast "MyChannel", set_dataset_property: [{
  element_id: "string", # required - the DOM element id of the element to be mutated
  name:       "string", # required - the property to set
  value:      "string", # [null]   - the value to assign to the dataset
}]
```

## Quick Start

> Please read the official [ActionCable docs](http://guides.rubyonrails.org/action_cable_overview.html) to learn more about ActionCable before proceeding.

```ruby
# app/models/user.rb
class User < ApplicationRecord
  include CableReady::Broadcaster

  def broadcast_name_change
    cable_ready_broadcast "UserChannel", text_content: [{ element_id: "user-name", text: name }]
  end
end
```

```javascript
// app/assets/javascripts/application.js
/*
 * ...
 *= require cable_ready
 * ...
 */
```

```javascript
// app/assets/javascripts/channels/user.js
App.cable.subscriptions.create({ channel: "UserChannel" }, {
  received: function (data) {
    if (data.cableReady) {
      CableReady.perform(data.operations);
    }
  }
});
```

## Advanced Usage

Consider using CableReady in concert with a gem like
[SelfRenderer](https://github.com/hopsoft/self_renderer) to create a powerful SPA style user experience with the simplicity of server side rendering.

<a target='_blank' rel='nofollow' href='https://app.codesponsor.io/link/QMSjMHrtPhvfmCnk5Hbikhhr/hopsoft/cable_ready'>
  <img alt='Sponsor' width='888' height='68' src='https://app.codesponsor.io/embed/QMSjMHrtPhvfmCnk5Hbikhhr/hopsoft/cable_ready.svg' />
</a>
