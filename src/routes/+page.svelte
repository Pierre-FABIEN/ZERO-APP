<script lang="ts">
	import '@fontsource-variable/oxanium';
	import '@fontsource/tomorrow';
	import '@fontsource/barlow';
	import '@fontsource-variable/advent-pro';

	import type { PageData } from './$types';

	let text = $state('');
	let index = $state(0);

	const phrase = 'Hello ZERO';
	const speed = 100; // vitesse en ms

	$effect(() => {
		if (index < phrase.length) {
			setTimeout(() => {
				text += phrase[index];
				index++;
			}, speed);
		}
	});
</script>

<div class="w-screen h-screen ccc">
	<section class="cyberpunk black both">
		<h1 class="cyberpunk glitched">{text}<span class="cursor">|</span></h1>
	</section>
	<button class="button cyberpunk blue">Continue</button>
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
