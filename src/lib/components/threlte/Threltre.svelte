<script lang="ts">
	import Scene from './Scene.svelte';
	import type { AsciiEffectOptions } from 'three/examples/jsm/Addons.js';
	import { AsciiRenderer } from '@threlte/extras';
	import { Canvas } from '@threlte/core';

	let fgColor = $state('#5e0808');
	let bgColor = $state('#00000000');

	const defaultCharacters = ' .:-+*=%@#';
	let characters = $state(defaultCharacters);

	let alpha = $state(1);
	let block = $state(true);
	let color = $state(false);
	let invert = $state(false);
	let resolution = $state(0.25);
	let scale = $state(1);

	const options: AsciiEffectOptions = $derived({
		alpha,
		block,
		color,
		invert,
		resolution,
		scale
	});

	let autoRotate = $state(true);
</script>

<div>
	<Canvas>
		<AsciiRenderer {bgColor} {characters} {fgColor} {options} />
		<Scene />
	</Canvas>
</div>

<style>
	div {
		height: 100vh;
		width: 100vw;
	}
</style>
