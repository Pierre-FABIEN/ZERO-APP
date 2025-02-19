<script lang="ts">
	import { goto } from '$app/navigation';
	import '@fontsource-variable/advent-pro';
	import Typewriter from 'typewriter-effect/dist/core';

	let h1Element: HTMLHeadingElement | null = null;

	$effect(() => {
		if (h1Element) {
			new Typewriter(h1Element, {
				loop: false,
				delay: 100,
				cursor: '|'
			})
				.typeString(
					"This is my last chance to achieve my dream. If I don't get it, I'll be lost in Liberty City."
				)
				.start();
		}
	});
</script>

<div class="w-screen h-screen ccc">
	<section class="cyberpunk black both">
		<h1 bind:this={h1Element} class="cyberpunk glitched"></h1>
	</section>

	<button
		class="button cyberpunk blue"
		onclick={() => {
			goto('/exemple');
		}}>Continue</button
	>
</div>

<style>
	.cursor {
		animation: blink 1s infinite;
	}

	@keyframes blink {
		50% {
			opacity: 0;
		}
	}

	h1 {
		width: 320px;
		text-align: center;
		span {
			font-weight: 900;
		}
	}

	section {
		background: transparent !important;
		&:before {
			background-color: transparent !important;
		}
		&:after {
			background: transparent !important;
		}
	}

	.button {
		margin-top: 100px;
		border: 1px solid red;
		background: transparent !important;
	}
</style>
