# Svelte

**Svelte** is a tool for building web applications. Like other user interface frameworks, it allows you to build your app declaratively out of components that combine markup, styles and behaviours.

## Setup

1. Let's use Vite to generate boilderplate for Svelte (not Svelte kit).
```bash
npm create vite@latest
```
2. Choose Svelte option as a framework.
3. Finally, install the packages: `npm install`.

## Components

Inside the `src/` folder, you would see `App.svelte` which is a component.

In Svelte, an application is composed from one or more components. A component is a reusable self-contained block of code that encapsulates HTML, CSS and JavaScript that belong together, written into a `.svelte` file.

Open the `App.svelte` file and replace everything with this basic code:
```svelte
<script>
	let name = 'World';
</script>

<div>
  <h1>Hello {name}!</h1>
</div>

<style>
h1 {
  color: red;
}
</style>
```

A component is divided into three parts, i.e., script, html and style. In the script tag, you can write javascript. In the style tag, you can write scoped css for the component. And the other part is the layout which is html.

Javascript can be used in `{}` (braces) to use them in html syntax.

## Nested Components
A component can be used in another component:
```svelte
<script>
	import Nested from './Nested.svelte';
</script>

<p>This is a paragraph.</p>
<Nested />

<style>
	p {
		color: goldenrod;
		font-family: 'Comic Sans MS', cursive;
		font-size: 2em;
	}
</style>
```

## HTML Tags
HTML can be parsed from a string in such a way using `@html` markup:
```svelte
<script>
	let string = `this string contains some <strong>HTML!!!</strong>`;
</script>

<p>{@html string}</p>
```

## State
Svelte is a powerful system of reactivity for keeping the DOM in sync with your application state — for example, in response to an event.
`$state` defined variables can be changed to update your page and sync it everywhere.
```svelte
<script>
	let count = $state(0);

	function increment() {
		count += 1;
	}
</script>

<button onclick={increment}>
	Clicked {count}
	{count === 1 ? 'time' : 'times'}
</button>
```
