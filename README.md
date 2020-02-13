`E` is a library which combines a eventBus/emitter, DOM events management, delegated events, and event-based utils into a single lightweight and performant library.

`E` works in all major browsers and IE11. It includes any polyfills it needs to work in IE11 by default.
* [Getting started](#getting-started)
* [Adding DOM Events](#dom-events)
* [Adding Delegated events](#delegated-events)
* [Removing events](#removing-events)
* [The Event Bus](#event-bus)
* [Binding handlers to maintain scope](#binding-handlers-to-maintain-scope)


## Getting started

`E` is packaged as a babel-ified UMD module which is safe to use via `require` or `import` syntax.

In order to use, `E` must be instantiated:

```js
import Events from '@unseenco/e'

const E = new Events()
```

If you don't care about IE11 and want to save a few bytes, then you could just bring in the un-compiled (and un-polyfilled) src code into your project:
```js
import Events from '@unseenco/e/src/es6'
```

And finally, if you want to be old-school, bring in the [CDN version](https://cdn.jsdelivr.net/npm/@unseenco/e@1.4.4/dist/e.js) to add `E` to the `window` (very lame).

## DOM Events

The `on` method attaches an event to one or many DOM elements with an easy to use API:

```js
E.on('click', '.js-open', callback)
E.on('resize', window, callback)

// Also accepts NodeLists/Arrays of elements
E.on('click', document.querySelectorAll('.btn'), callback)

// With a HTMLElement
E.on('click', document.getElementById('unique'), callback)
```

You can also add a callback to multiple events at once:
```js
E.on('click keyup', '.js-open', callback)
```



## Delegated Events
Events bound with `delegate` are bound to the `document` instead of the element, which removes the need to rebind/remove events during page transitions, or when the DOM updates after load.

Intercepted events are dispatched to the correct handler using [Selector Set](https://github.com/josh/selector-set), which matches the event target element [incredibly efficiently](https://github.com/josh/selector-set#inspired-by-browsers).

The `delegate` method currently only accepts a selector string to match elements:
```js
E.delegate('click', '.js-open', callback)
```

You can delegate a callback to multiple events at once:
```js
E.delegate('input keyup', '.js-input', callback)
```

## Removing Events
You can remove a bound handler using the `off` method. The arguments are exactly the same as the `on` method, and events can be removed by passing a `string`, `HTMLElement`, or a `NodeList`.

```js
E.off('click', '.js-open', callback)
```

If an element has the same callback for multiple events, you can remove them all at once:
```js
E.off('click focus', '.js-open', callback)
```

## Event Bus
The API for the event bus uses the exact same methods as above, but without supplying a DOM element.

#### Registering a bus event
Use the `on` method to register an event and a listener. As many listeners can be subscribed to your event as you like.
```js
E.on('my.bus.event', callback)
```

#### Emitting a bus event
Use the `emit` method without an element will attempt to dispatch a bus event. If one exists, all listeners will be run in the order they were originally added:
```js
E.emit('my.bus.event')

// you can also pass arguments through
E.emit('my.bus.event', arg1, arg2)
```

#### Removing a listener from a bus event
You can subscribe one or all events from the bus using `off`:

```js
// Will remove the supplied callback if found
E.off('my.bus.event', callback)

// Will remove all listeners for the bus event
E.off('my.bus.event')
```

## Binding handlers to maintain scope
There are many ways to ensure that your event handlers keep the correct context when working with OO.

#### Closure method (preferred)

Probably the simplest method way to keep scope in handlers:

```js
// Include @babel/plugin-proposal-class-properties for property support in classes
class Foo {
    bar = (e) => {
        console.log(this)
    }
}
```

#### Using `bindAll`

`Unseen.e` has a handy `bindAll` method if you prefer to do it the old fashioned way:
```js
class Foo {
    constructor() {
        E.bindAll(this, ['bar'])
    }

    bar() {
        console.log(this)
    }
}
```

You can also call `bindAll` without providing any methods to automatically bind all public methods to the current instance:

```js
class Foo {
    constructor() {
        // Will bind bar, but not privateBar
        E.bindAll(this)
    }

    bar() {
        console.log(this)
    }
    
    #privateBar() {
    
    }
}
```

