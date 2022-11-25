# Template Syntax

If you are familiar with Virtual DOM concepts and prefer the raw power of JavaScript, you can also directly write render functions instead of templates, with optional JSX support. However, do note that they do not enjoy the same level of compile-time optimizations as templates.

> Text Interpolation

```html
	<span>Message: {{ msg }}</span>
```
> Raw HTML

```html
	<p>Using text interpolation: {{ rawHtml }}</p>
	<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```
**⚠ Security Warning:**  
Dynamically rendering arbitrary HTML on your website can be very dangerous because it can easily lead to XSS vulnerabilities. Only use v-html on trusted content and never on user-provided content.

> Attribute Bindings

```html
	<div v-bind:id="dynamicId"></div>
```

The v-bind directive instructs Vue to keep the element's id attribute in sync with the component's dynamicId property. If the bound value is null or undefined, then the attribute will be removed from the rendered element.

- Shorthand

```html
	<div :id="dynamicId"></div>
```

> Boolean Attributes

Boolean attributes are attributes that can indicate true / false values by its presence on an element. For example, disabled is one of the most commonly used boolean attributes.

v-bind works a bit differently in this case:

```html
	<button :disabled="isButtonDisabled">Button</button>
```
The disabled attribute will be included if isButtonDisabled has a truthy value. It will also be included if the value is an empty string, maintaining consistency with ```html <button disabled=""> ```. 
For other falsy values the attribute will be omitted.

> Dynamically Binding Multiple Attributes

```html
	<div v-bind="objectOfAttrs"></div>
```

> Using JavaScript Expressions

```html

{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>

```

> Expressions Only

Each binding can only contain one single expression. An expression is a piece of code that can be evaluated to a value. A simple check is whether it can be used after return.

Therefore, the following will NOT work:

```html

<!-- this is a statement, not an expression: -->
	{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
	{{ if (ok) { return message } }}

```

> Calling Functions

It is possible to call a component-exposed method inside a binding expression:

```html
	<span :title="toTitleDate(date)">
		{{ formatDate(date) }}
	</span>
```

**⚠TIP**
Functions called inside binding expressions will be called every time the component updates, so they should not have any side effects, such as changing data or triggering asynchronous operations.

> Restricted Globals Access

```js
const GLOBALS_WHITE_LISTED =
  'Infinity,undefined,NaN,isFinite,isNaN,parseFloat,parseInt,decodeURI,' +
  'decodeURIComponent,encodeURI,encodeURIComponent,Math,Number,Date,Array,' +
  'Object,Boolean,String,RegExp,Map,Set,JSON,Intl,BigInt'
```
Template expressions are sandboxed and only have access to a restricted list of globals. The list exposes commonly used built-in globals such as Math and Date.

Globals not explicitly included in the list, for example user-attached properties on window, will not be accessible in template expressions. You can, however, explicitly define additional globals for all Vue expressions by adding them to app.config.globalProperties.

```js
	interface AppConfig {
  		globalProperties: Record<string, any>
	}

	app.config.globalProperties.msg = 'hello'
```

> Directives

Directives are special attributes with the v- prefix. Vue provides a number of built-in directives, including v-html and v-bind which we have introduced above.

Directive attribute values are expected to be single JavaScript expressions (with the exception of v-for, v-on and v-slot, which will be discussed in their respective sections later). A directive's job is to reactively apply updates to the DOM when the value of its expression changes. Take v-if as an example:

```html
	<p v-if="seen">Now you see me</p>
```

> Arguments

Some directives can take an "argument", denoted by a colon after the directive name. For example, the v-bind directive is used to reactively update an HTML attribute:

```html
	<a v-bind:href="url"> ... </a>

	<!-- shorthand -->
	<a :href="url"> ... </a>
```

Here, href is the argument, which tells the v-bind directive to bind the element's href attribute to the value of the expression url. In the shorthand, everything before the argument (i.e., v-bind:) is condensed into a single character, :.

Another example is the v-on directive, which listens to DOM events:

```html
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>
```

> Dynamic Arguments

It is also possible to use a JavaScript expression in a directive argument by wrapping it with square brackets:

```html
	<a v-bind:[attributeName]="url"> ... </a>

	<!-- shorthand -->
	<a :[attributeName]="url"> ... </a>
```

Here, attributeName will be dynamically evaluated as a JavaScript expression, and its evaluated value will be used as the final value for the argument. For example, if your component instance has a data property, attributeName, whose value is "href", then this binding will be equivalent to v-bind:href.

Similarly, you can use dynamic arguments to bind a handler to a dynamic event name:

```html

	<a v-on:[eventName]="doSomething"> ... </a>

	<!-- shorthand -->
	<a @[eventName]="doSomething">
```

> Dynamic Argument Value Constraints

Dynamic arguments are expected to evaluate to a string, with the exception of null. The special value null can be used to explicitly remove the binding. Any other non-string value will trigger a warning.

> Dynamic Argument Syntax Constraints

Dynamic argument expressions have some syntax constraints because certain characters, such as spaces and quotes, are invalid inside HTML attribute names. For example, the following is invalid:
```html
<!-- This will trigger a compiler warning. -->
<a :['foo' + bar]="value"> ... </a>
```

If you need to pass a complex dynamic argument, it's probably better to use a computed property, which we will cover shortly.

When using in-DOM templates (templates directly written in an HTML file), you should also avoid naming keys with uppercase characters, as browsers will coerce attribute names into lowercase:

```html

<a :[someAttr]="value"> ... </a>

```
The above will be converted to :[someattr] in in-DOM templates. If your component has a someAttr property instead of someattr, your code won't work. Templates inside Single-File Components are not subject to this constraint.

> Modifiers

Modifiers are special postfixes denoted by a dot, which indicate that a directive should be bound in some special way. For example, the .prevent modifier tells the v-on directive to call event.preventDefault() on the triggered event:

```html
<form @submit.prevent="onSubmit">...</form>
```