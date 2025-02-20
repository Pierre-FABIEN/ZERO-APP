<script lang="ts">
	import { goto } from '$app/navigation';
	import '@fontsource-variable/advent-pro';
	import Typewriter from 'typewriter-effect/dist/core';

	let h1Element: HTMLHeadingElement | null = null;
	let audioElement: HTMLAudioElement | null = null;

	// Chargement de l'audio pour le hover du bouton
	let LastChanceAudio: HTMLAudioElement | null = null;
	$effect(() => {
		LastChanceAudio = new Audio('/audio/voice/LastChance.wav');
		LastChanceAudio.preload = 'auto';
		LastChanceAudio.volume = 0.8;
	});

	// Jouer "HelloZero" au hover du bouton
	function playLastChanceAudio() {
		if (LastChanceAudio) {
			LastChanceAudio.currentTime = 0;
			LastChanceAudio.play().catch((err) => console.error('Erreur de lecture audio:', err));
		}
	}

	$effect(() => {
		if (h1Element) {
			new Typewriter(h1Element, {
				loop: false,
				delay: 100,
				cursor: '|'
			})
				.typeString(
					"It's my last chance to achieve my dream. If I don't get it, I'll be lost in Liberty City. #NO-JOKE"
				)
				.start();
		}
	});

	// Chargement de l’audio une seule fois
	$effect(() => {
		audioElement = new Audio('/audio/click.wav'); // Mets ton fichier ici
		audioElement.preload = 'auto'; // Précharge l’audio
		audioElement.volume = 0.7; // Ajuste le volume si nécessaire
	});

	// Fonction pour jouer le son au hover
	function playHoverSound() {
		if (audioElement && audioElement.paused) {
			audioElement.currentTime = 0;
			audioElement.play().catch((err) => console.error('Audio play error:', err));
		}
	}
</script>

<div class="w-screen h-screen ccc">
	<section class="cyberpunk black both">
		<hr class="cyberpunk glitched" />
		<h3 bind:this={h1Element} class="cyberpunk glitched"></h3>
	</section>

	<button
		class="button cyberpunk blue"
		onmouseenter={playLastChanceAudio}
		onclick={() => {
			playHoverSound();
			goto('/contact');
		}}>Continue</button
	>
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

	h3 {
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
