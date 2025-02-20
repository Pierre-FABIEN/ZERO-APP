<script lang="ts">
	import { goto } from '$app/navigation';
	import '@fontsource-variable/advent-pro';
	import Typewriter from 'typewriter-effect/dist/core';

	let h1Element: HTMLHeadingElement | null = null;
	let clickAudio: HTMLAudioElement | null = null;
	let helloZeroAudio: HTMLAudioElement | null = null;

	// Chargement de l'audio pour le hover du bouton
	$effect(() => {
		helloZeroAudio = new Audio('/audio/voice/HelloZero.wav');
		helloZeroAudio.preload = 'auto';
		helloZeroAudio.volume = 0.8;
	});

	// Chargement de l'audio pour le clic
	$effect(() => {
		clickAudio = new Audio('/audio/click.wav');
		clickAudio.preload = 'auto';
		clickAudio.volume = 0.7;
	});

	// Jouer "HelloZero" au hover du bouton
	function playHelloZero() {
		if (helloZeroAudio) {
			helloZeroAudio.currentTime = 0;
			helloZeroAudio.play().catch((err) => console.error('Erreur de lecture audio:', err));
		}
	}

	// Jouer le son du clic
	function playClickSound() {
		if (clickAudio) {
			clickAudio.currentTime = 0;
			clickAudio.play().catch((err) => console.error('Erreur de lecture audio:', err));
		}
	}

	// Effet machine à écrire
	$effect(() => {
		if (h1Element) {
			new Typewriter(h1Element, {
				loop: false,
				delay: 100,
				cursor: '|'
			})
				.typeString('Hello ZERO')
				.start();
		}
	});
</script>

<div class="w-screen h-screen ccc">
	<section class="cyberpunk black both">
		<hr class="cyberpunk glitched" />
		<h1 bind:this={h1Element} class="cyberpunk glitched"></h1>
	</section>

	<button
		class="button cyberpunk blue"
		onmouseenter={playHelloZero}
		onclick={() => {
			playClickSound();
			goto('/my-application');
		}}
	>
		>Continue
	</button>
</div>

<style lang="scss">
	.cursor {
		animation: blink 1s infinite;
	}

	@keyframes blink {
		50% {
			opacity: 0;
		}
	}

	h1 {
		text-align: center;
		font-size: 50px;
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
