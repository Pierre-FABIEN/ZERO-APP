<script lang="ts">
	import gsap from 'gsap';
	import { tick } from 'svelte';
	import { get } from 'svelte/store';
	import {
		loadingStates,
		setDomLoaded,
		setFirstLoadComplete,
		setEnableAudio
	} from '$store/initialLoaderStore';

	let initalLoader: HTMLElement;
	let observer: IntersectionObserver;

	// We will show the "Continue" button after all states are true
	let showContinue = $state(false);

	$effect(() => {
		tick().then(() => {
			setupObserver();
		});
		return () => {
			if (observer) {
				observer.disconnect();
			}
		};
	});

	async function setupObserver() {
		observer = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					if (entry.isIntersecting) {
						// The loader is visible => we mark domLoaded
						setDomLoaded(true);
						animateIn();
						observer.unobserve(initalLoader);
					}
				});
			},
			{ threshold: 0.5 }
		);
		observer.observe(initalLoader);
	}

	function animateIn() {
		if (!initalLoader) return;
		gsap.fromTo(
			initalLoader.querySelectorAll('.letter'),
			{ opacity: 0, y: 20 },
			{
				opacity: 1,
				y: 0,
				duration: 0.2,
				stagger: 0.1,
				ease: 'power2.out',
				onComplete: checkLoadingComplete
			}
		);
	}

	function checkLoadingComplete() {
		const currentLoadingStates = get(loadingStates);

		// Déstructurer seulement les 3 propriétés nécessaires
		const { firstOpen, domLoaded, ressourceToValide } = currentLoadingStates;

		if (firstOpen && domLoaded && ressourceToValide) {
			showContinue = true;
		} else {
			// Réessaye dans 200 ms
			setTimeout(checkLoadingComplete, 200);
		}
	}

	function onClickContinue() {
		// Activer l’audio
		setEnableAudio(true);

		// Passer en plein écran
		toggleFullScreen();

		// Animer la disparition du loader
		animateOut();
	}

	function toggleFullScreen() {
		if (!document.fullscreenElement) {
			// Entrer en plein écran
			document.documentElement.requestFullscreen().catch((err) => {
				console.error(`Erreur en activant le plein écran: ${err.message}`);
			});
		} else {
			// Quitter le plein écran si déjà actif (optionnel)
			document.exitFullscreen();
		}
	}

	function animateOut() {
		if (!initalLoader) return;
		gsap.to(initalLoader.querySelectorAll('.letter'), {
			opacity: 0,
			y: -20,
			duration: 0.2,
			stagger: 0.1,
			ease: 'power2.in',
			onComplete: () => {
				// Once animation ends, mark the first load as complete
				setFirstLoadComplete(true);
			}
		});
	}
</script>

<div class="initalLoader flex justify-center items-center" bind:this={initalLoader}>
	<!-- Animated Letters -->
	<div class="rcc">
		{#each Array.from('Welcome') as letter, i (letter + i)}
			<span class="letter">{letter}</span>
		{/each}
	</div>

	<!-- The "Continue" button, shown only after all states are true -->
	{#if showContinue}
		<button class="continue-btn" onclick={onClickContinue}> Start </button>
	{/if}
</div>

<style lang="scss">
	.letter {
		opacity: 0;
	}
	.initalLoader {
		background-color: #ff470a;
		position: absolute;
		top: 0;
		right: 0;
		z-index: 200;
		width: 100vw;
		height: 100vh;
		color: white;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.continue-btn {
		margin-top: 2rem;
		background-color: #000;
		color: #fff;
		padding: 1rem 2rem;
		border: none;
		border-radius: 5px;
		font-size: 1.2rem;
		cursor: pointer;
		transition: background 0.3s;

		&:hover {
			background-color: #222;
		}
	}
</style>
