# Composition API

The Composition API is centered around declaring reactive state variables directly in a function scope and composing state from multiple functions together to handle complexity. It is more free-form and requires an understanding of how reactivity works in Vue to be used effectively. In return, its flexibility enables more powerful patterns for organizing and reusing logic.

## The Root Component

The object we are passing into createApp is in fact a component. Every app requires a "root component" that can contain other components as its children.

## App Configurations

The application instance exposes a .config object that allows us to configure a few app-level options, for example, defining an app-level error handler that captures errors from all descendant components:

```js
	app.config.errorHandler = (err) => {
	/* handle error */
	}
```

The application instance also provides a few methods for registering app-scoped assets. For example, registering a component:

```js
app.component('TodoDeleteButton', TodoDeleteButton)
```

This makes the TodoDeleteButton available for use anywhere in our app

Make sure to apply all app configurations before mounting the app!

## Multiple application instances

You are not limited to a single application instance on the same page. The createApp API allows multiple Vue applications to co-exist on the same page, each with its own scope for configuration and global assets:

```js
	const app1 = createApp({
  		/* ... */
	})
	app1.mount('#container-1')

	const app2 = createApp({
  		/* ... */
	})
	app2.mount('#container-2')
```