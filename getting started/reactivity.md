# Reactive Proxy vs. Original

In Vue 3, data is made reactive by leveraging JavaScript Proxies. Users coming from Vue 2 should be aware of the following edge case:

```js

export default {
  data() {
    return {
      someObject: {}
    }
  },
  mounted() {
    const newObject = {}
    this.someObject = newObject

    console.log(newObject === this.someObject) // false
  }
}
```

When you access this.someObject after assigning it, the value is a reactive proxy of the original newObject. Unlike in Vue 2, the original newObject is left intact and will not be made reactive: make sure to always access reactive state as a property of this.

## Methods

Vue automatically binds the this value for methods so that it always refers to the component instance. This ensures that a method retains the correct this value if it's used as an event listener or callback. You should avoid using arrow functions when defining methods, as that prevents Vue from binding the appropriate this value:

```js

export default {
  methods: {
    increment: () => {
      // BAD: no `this` access here!
    }
  }
}

```

## Deep Reactivity

In Vue, state is deeply reactive by default. This means you can expect changes to be detected even when you mutate nested objects or arrays:

It is also possible to explicitly create **shallow reactive objects** where the reactivity is only tracked at the root-level, but these are typically only needed in advanced use cases.

## Stateful Methods
In some cases, we may need to dynamically create a method function, for example creating a debounced event handler:

```js
import { debounce } from 'lodash-es'

export default {
  methods: {
    // Debouncing with Lodash
    click: debounce(function () {
      // ... respond to click ...
    }, 500)
  }
}
```
However, this approach is problematic for components that are reused because a debounced function is stateful: it maintains some internal state on the elapsed time. If multiple component instances share the same debounced function, they will interfere with one another.

To keep each component instance's debounced function independent of the others, we can create the debounced version in the created lifecycle hook:

```js
export default {
  created() {
    // each instance now has its own copy of debounced handler
    this.debouncedClick = _.debounce(this.click, 500)
  },
  unmounted() {
    // also a good idea to cancel the timer
    // when the component is removed
    this.debouncedClick.cancel()
  },
  methods: {
    click() {
      // ... respond to click ...
    }
  }
}

```
```html
```
```html
```
```html
```
```html
```