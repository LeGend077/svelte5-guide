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

## Dynamic Attributes
Just like you can use curly braces to control text, you can use them to control element attributes.
```svelte
<script>
	let src = '/tutorial/image.gif';
	let name = 'Rick Astley';
</script>

<img {src} alt="{name} dances." />
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

### Deep State
As we saw in the previously, state reacts to reassignments. But it also reacts to mutations — we call this deep reactivity.
```svelte
<script>
	let numbers = $state([1, 2, 3, 4]);

	function addNumber() {
		numbers.push(numbers.length + 1);
	}
</script>

<p>{numbers.join(' + ')} = ...</p>

<button onclick={addNumber}>
	Add a number
</button>
```

### Derived State
Sometimes we want a state to be dependent on the value of another state. For this reason, we use `$derived`:
```svelte
<script>
	let numbers = $state([1, 2, 3, 4]);
	let total = $derived(numbers.reduce((t, n) => t + n, 0));

	function addNumber() {
		numbers.push(numbers.length + 1);
	}
</script>

<p>{numbers.join(' + ')} = {total}</p>

<button onclick={addNumber}>
	Add a number
</button>
```

The expression inside the $derived declaration will be re-evaluated whenever its dependencies (in this case, just `numbers`) are updated.

### Inspecting State
It is useful to see your state in the console to track them but directly passing them to a log gives a cloning warning. To properly log a state, we take a snapshot of the state to log it.
```svelte
function addNumber() {
	numbers.push(numbers.length + 1);
	console.log($state.snapshot(numbers));
}
```

Moreover, you can autmatically log a snapshot of a state whenever the state is updated using `$inspect`:
```svelte
function addNumber() {
	numbers.push(numbers.length + 1);
	console.log($state.snapshot(numbers));
}

$inspect(numbers);
```

You can customise how the information is displayed by using `$inspect(...).with(fn)` — for example, you can use `console.trace` to see where the state change originated from:
```svelte
<script>
	let numbers = $state([1, 2, 3, 4]);
	let total = $derived(numbers.reduce((t, n) => t + n, 0));

	function addNumber() {
		numbers.push(numbers.length + 1);
	}

	$inspect(numbers).with(console.trace);
</script>

<p>{numbers.join(' + ')} = {total}</p>

<button onclick={addNumber}>
	Add a number
</button>
```

### Effects
When the value of a state is changed, the elements react to it and update accordingly. But sometimes we have to run something when value of state or multiple states is changed. We use `$effect` to keep track of change in states:
```svelte
<script>
	let size = $state(50);
	let color = $state('#ff3e00');
	let a = $state(false);
	
	let canvas;

	$effect(() => {
		const context = canvas.getContext('2d');
		context.clearRect(0, 0, canvas.width, canvas.height);

		// this will re-run whenever `color` or `size` change
		context.fillStyle = color;
		context.fillRect(0, 0, size, size);
		console.log('I ran')
	});
</script>

<canvas bind:this={canvas} width="100" height="100"></canvas>

<div class="controls">
	<label>
		<input type="range" bind:value={size} /> size
	</label>
	
	<label>
		<input type="color" bind:value={color} /> color
	</label>

	<button onclick={() => a = !a}>Click Me!</button>
</div>

<style>
	.controls {
		border-top: 1px solid #ccc;
		padding: 1em 0;
		margin: 1em 0;
		display: flex;
		gap: 1em;
	}
	
	label {
		display: flex;
		align-items: center;
		gap: 0.5em;
	}
</style>
```

You can see the code inside `$effect` runs only when `size` or `color` state is updated because it uses those states inside it. Change in `a` does not execute that code.

### Universal/Shared States
Sometimes we want to use a state in multiple components and update them in all other components. Using runes is not allowed in normal `.js` files so we create a `shared.svelte.js` to create a exported state:
```svelte
export const counter = $state({
	count: 0
});
```

Now this state can be imported to any component for usage:
```svelte
<script>
	import { counter } from './shared.svelte.js';
</script>

<button onclick={() => counter.count += 1}>
	clicks: {counter.count}
</button>
```

## Properties
### Declaring Properties
So far, we’ve dealt exclusively with internal state — that is to say, the values are only accessible within a given component.

In any real application, you’ll need to pass data from one component down to its children. To do that, we need to declare properties, generally shortened to ‘props’. In Svelte, we do that with the `$props` rune. Edit the `Nested.svelte` component:
```svelte
<script>
	let { answer } = $props();
</script>

<p>The answer is {answer}</p>
```

Now in your `App.svelte`, you can import the component and pass the property as an attribute:
```svelte
<script>
	import Nested from './Nested.svelte';
</script>

<Nested answer={42} />
```

You can also give your properties a default value:
```svelte
<script>
	let { answer = 'a mystery' } = $props();
</script>

<p>The answer is {answer}</p>
```

### Spread Props
If your	 component has many props, you can spread a object containing the props:
```svelte
<script>
	let { name, version, description, website } = $props();
</script>

<p>
	The <code>{name}</code> package is {description}. Download version {version} from
	<a href="https://www.npmjs.com/package/{name}">npm</a> and <a href={website}>learn more here</a>
</p>
```
In `App.svelte`:
```svelte
<script>
	import PackageInfo from './PackageInfo.svelte';

	const pkg = {
		name: 'svelte',
		version: 5,
		description: 'blazing fast',
		website: 'https://svelte.dev'
	};
</script>

<PackageInfo {...pkg} />
```

## Logic
HTML doesn’t have a way of expressing logic, like conditionals and loops. Svelte does.
