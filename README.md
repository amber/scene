Scene
=====

CoffeeScript user interface framework inspired by [space-pen](http://atom.github.io/space-pen/).

Builder
-------

Scene uses a simple markup DSL for building views. The `$$` function constructs a document fragment:

```coffeescript
document.body.appendChild $$ ->
  @h1 "Scene"
  @ol =>
    @li "Simple"
    @li "Concise"
    @li "Reusable"
```

You can add attributes and event handlers by passing a map to a tag constructor:

```coffeescript
document.body.appendChild $$ ->
  @p class: "example", =>
    @button "Click me", click: -> alert "Hello, world!"
```

Views
-----

Views are a simple wrapper around a DOM node.

```coffeescript
class Login extends View
  @content: ->
    @section =>
      @h1 "Sign in"
      @input placeholder: "Username"
      @input type: "password", placeholder: "Password"
      @button "Sign in"
```

You can add them to the DOM with the `embed` method:

```coffeescript
new Login().embed document.body
```

Event handlers can be names of methods on the view. You can refer to nodes using the `outlet` attribute:

```coffeescript
class Login extends View
  @content: ->
    @section =>
      @h1 "Sign in"
      @input outlet: "user", placeholder: "Username"
      @input type: "password", placeholder: "Password"
      @button "Sign in", click: "submit"
  
  submit: ->
    alert "Hello, #{@user.value}!"
```

Views can include subviews in their content with the `@subview` builder method, which takes an optional outlet name:

```coffeescript
class Header extends View
  @content: ->
    @header =>
      @h1 "Scene"
      @subview "login", new Login
```

Subviews are stored in a view's `subviews` property. Each view stores a reference to its parent view in the `parent` property. You can use the `add`, `remove`, and `replaceWith` methods to manipulate subviews:

```coffeescript
class Header extends View
  @content: ->
    @header =>
      @h1 "Scene"
      @subview "login", new Login

  addNotice: (n) ->
    if @notice?
      @notice.replaceWith n
    else
      @add @notice = n
    setTimeout @removeNotice, 5000

  removeNotice: =>
    @notice.remove() if @notice?
```

By default, `add` inserts the subview as a child of `@base`, the view's root node. You can pass a different mounting point as the second parameter, and a child node to insert the subview before as the third.

The `@content` class method and the `initialize` instance method are passed any constructor arguments:

```coffeescript
class Warning extends View
  @content: ({message}) ->
    @p class: "warning", "Warning: #{message}"

header = new Header().embed document.body
header.addNotice new Warning message: "this is a warning"
```
