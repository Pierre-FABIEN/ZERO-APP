<script lang="ts">
	import '@fontsource-variable/advent-pro';
	import Typewriter from 'typewriter-effect/dist/core';

	let h1Element: HTMLHeadingElement | null = null;

	$effect(() => {
		if (h1Element) {
			new Typewriter(h1Element, {
				loop: false,
				delay: 300,
				cursor: '|'
			})
				.typeString('Hello ZERO')
				.start();
		}
	});
</script>

<div class="w-screen h-screen ccc">
	<section class="cyberpunk black both">
		<h1 bind:this={h1Element} class="cyberpunk glitched"></h1>
	</section>
	<a href="/application">
		<button class="button cyberpunk blue">Continue</button>
	</a>
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
	}

	section {
		background: transparent !important;
	}

	.button {
		margin-top: 100px;
		border: 1px solid red;
		background: transparent !important;
	}
</style>
