<script lang="ts">
	import { render } from '$lib';
	import { SvelteDate } from 'svelte/reactivity';

	let date = new SvelteDate();

	const start = new Date('2026-01-15T10:51:00Z');
	const patches = 34;

	let value = $state('');
	let copied = $state(false);
	let copied_timeout: ReturnType<typeof setTimeout>;

	const version = $derived.by(() => {
		let major = 1;
		let minor = 1;
		let patch = 0;
		const time = (date.getTime() - start.getTime()) / 3000;
		const minors = time / patches;
		minor += Math.floor(minors);
		patch += Math.floor((minors - Math.floor(minors)) * patches);
		return `${major}.${minor}.${patch}`;
	});

	$effect(() => {
		const interval = setInterval(() => {
			date.setTime(Date.now());
		}, 1000);
		return () => {
			clearInterval(interval);
		};
	});
</script>

<label>
	<a class="github" href="https://github.com/paoloricciuti/openlogo">github</a>
	<h1><span>{render('open')}</span><span>{render('logo')}</span></h1>
	<section>
		<textarea id="input" bind:value placeholder="Ask anything..."></textarea>
		<span class="agent">Build</span>
		<span>Claude Opus 4.5</span>
		<span class="provider">OpenCode Zen</span>
	</section>
	<button
		onclick={async () => {
			if (value) {
				clearTimeout(copied_timeout);
				await navigator.clipboard.writeText(render(value));
				copied = true;
				copied_timeout = setTimeout(() => {
					copied = false;
				}, 1000);
			}
		}}>{render(value)}<span class={['hint', { copied }]}>click to copy</span></button
	>
	<span class="cwd">~/code/openlogo</span>
	<span class="version">{version}</span>
</label>

<style>
	label {
		display: grid;
		place-items: center;
		place-content: center;
		gap: 2rem;
		height: 100vh;
		font-family: 'JetBrains Mono Variable', monospace;
	}
	h1,
	button {
		all: unset;
		white-space: pre;
		display: flex;
		font-size: 1rem;
		font-family: 'JetBrains Mono Variable', monospace;
		line-height: 1;

		:first-child {
			color: #808080;
		}
	}
	button {
		cursor: pointer;
		font-size: 0.7rem;
		padding: 1rem;
		width: min(80vw, 50rem);
		position: relative;
		background-color: #1e1e1e;
		height: 4lh;
		.hint {
			position: absolute;
			right: 0.5rem;
			bottom: 0.5rem;
			transition: color 250ms;
			&.copied {
				color: var(--accent);
			}
		}
	}
	section {
		background-color: #1e1e1e;
		border-left: var(--accent) 4px solid;
		padding: 1rem;
		font-family: 'JetBrains Mono Variable', monospace;
		display: grid;
		grid-template-columns: min-content min-content 1fr;
		column-gap: 1rem;
		span {
			white-space: nowrap;
		}
		.agent {
			color: var(--accent);
		}
		.provider {
			opacity: 0.5;
		}
		textarea {
			font-size: 1rem;
			grid-column: 1/-1;
			background-color: transparent;
			color: var(--foreground);
			border: 0;
			resize: none;
			width: min(80vw, 50rem);
			font-family: 'JetBrains Mono Variable', monospace;
			outline: none;
		}
	}
	.cwd,
	.version,
	.github {
		--padding: 1rem;
		position: fixed;
		bottom: var(--padding);
		left: var(--padding);
		opacity: 0.3;
		text-decoration: none;
		color: inherit;
	}
	.version {
		left: unset;
		right: var(--padding);
	}
	.github {
		bottom: unset;
		left: unset;
		top: var(--padding);
		right: var(--padding);
	}
</style>
