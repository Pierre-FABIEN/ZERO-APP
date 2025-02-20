<script lang="ts">
	import { onMount } from 'svelte';
	import { initializeLayoutState, setupNavigationEffect, isClient } from './layout.svelte';
	import { ModeWatcher } from 'mode-watcher';
	import { Toaster } from '$shadcn/sonner';
	import '../app.css';
	import SmoothScrollBar from '$lib/components/smoothScrollBar/SmoothScrollBar.svelte';
	import {
		firstLoadComplete,
		setFirstOpen,
		setRessourceToValide,
		loadingStates
	} from '$lib/store/initialLoaderStore';
	import Loader from '$lib/components/loader/Loader.svelte';
	import { page } from '$app/stores';
	import Threltre from '$lib/components/threlte/Threltre.svelte';

	/* ----------------------------
	 *   CONSTANTES DE BOUCLE
	 * ---------------------------- */
	const LOOP_START = 0; // Début de la boucle (en secondes)
	const LOOP_END = 6.0; // Fin de la boucle (en secondes)
	const FADE_DURATION = 0.1; // 300ms pour le fade-in/out

	/* ----------------------------
	 *   Objets Web Audio
	 * ---------------------------- */
	let audioCtx: AudioContext | null = null;
	let audioBuffer: AudioBuffer | null = null;
	let sourceNode: AudioBufferSourceNode | null = null;
	let gainNode: GainNode | null = null;

	let { children } = $props();

	/* ------------------------------------------------------------------
	   1) CHARGEMENT DE L'AUDIO LORS DU MONTAGE
	   ------------------------------------------------------------------ */
	onMount(async () => {
		if (!isClient) return; // pas de chargement côté serveur

		try {
			audioCtx = new AudioContext();

			// Chargement du fichier audio (idéalement un .wav ou .ogg sans blanc)
			const response = await fetch('/audio/bg.wav');
			const data = await response.arrayBuffer();
			audioBuffer = await audioCtx.decodeAudioData(data);

			if (audioCtx.state === 'suspended') {
				console.log('AudioContext suspendu, besoin d’une interaction user');
			}
		} catch (err) {
			console.error('Erreur de chargement/décodage audio :', err);
		}
	});

	/* ------------------------------------------------------------------
	   2) INIT DU LAYOUT : premières actions + cleanup
	   ------------------------------------------------------------------ */
	$effect(() => {
		const unsubPage = page.subscribe((currentPage) => {
			initializeLayoutState(currentPage);
		});
		setupNavigationEffect();

		setFirstOpen(true);
		setRessourceToValide(true);

		return () => {
			unsubPage();
			stopLoop(); // On coupe l'audio en quittant la page
			if (audioCtx && audioCtx.state !== 'closed') {
				audioCtx.close();
			}
		};
	});

	/* ------------------------------------------------------------------
	   3) LISTEN STORE LOADINGSTATES : enableAudio => start/stop loop
	   ------------------------------------------------------------------ */
	$effect(() => {
		const unsubLoading = loadingStates.subscribe(($states) => {
			if (!isClient || !audioCtx || !audioBuffer) return;

			if ($states.enableAudio) {
				startLoop();
			} else {
				stopLoop();
			}
		});

		return () => {
			unsubLoading();
		};
	});

	/* ------------------------------------------------------------------
	   FONCTION startLoop : crée un sourceNode, gère loop et fade-in
	   ------------------------------------------------------------------ */
	async function startLoop() {
		if (!audioCtx || !audioBuffer) return;

		// Si déjà une lecture en cours, on arrête
		stopLoop();

		// Reprend le contexte si bloqué
		if (audioCtx.state === 'suspended') {
			await audioCtx.resume();
		}

		// Crée un buffer source + un gain node
		sourceNode = audioCtx.createBufferSource();
		gainNode = audioCtx.createGain();

		sourceNode.buffer = audioBuffer;
		sourceNode.loop = true;
		sourceNode.loopStart = LOOP_START;
		sourceNode.loopEnd = LOOP_END;

		// Au départ, volume = 0 pour éviter clic
		gainNode.gain.setValueAtTime(0, audioCtx.currentTime);
		// Fade-in sur FADE_DURATION
		gainNode.gain.linearRampToValueAtTime(1, audioCtx.currentTime + FADE_DURATION);

		// Connexions : source -> gain -> destination
		sourceNode.connect(gainNode);
		gainNode.connect(audioCtx.destination);

		sourceNode.start(0, LOOP_START);
		console.log(`Lecture loop de ${LOOP_START}s à ${LOOP_END}s`);
	}

	/* ------------------------------------------------------------------
	   FONCTION stopLoop : fade-out progressif, puis stop et disconnect
	   ------------------------------------------------------------------ */
	function stopLoop() {
		if (!sourceNode || !gainNode || !audioCtx) return;

		const now = audioCtx.currentTime;
		gainNode.gain.setValueAtTime(gainNode.gain.value, now);
		gainNode.gain.linearRampToValueAtTime(0, now + FADE_DURATION);

		setTimeout(() => {
			sourceNode?.stop();
			sourceNode?.disconnect();
			gainNode?.disconnect();
			sourceNode = null;
			gainNode = null;
		}, FADE_DURATION * 1000);
	}
</script>

<svelte:head>
	<link rel="icon" href="/favicon.png" />
	<meta name="viewport" content="width=device-width" />
	<link rel="manifest" href="/pwa/manifest.webmanifest" />
	<meta name="theme-color" content="#4285f4" />
</svelte:head>

<!-- LOADER si pas encore "complete" -->
{#if !$firstLoadComplete}
	<Loader />
{/if}

{#if $isClient}
	<div class="wappper">
		<ModeWatcher />
		<div class="canva">
			<Threltre />
		</div>
		<div class="container">
			<SmoothScrollBar>
				<main class="mainLayout">
					{@render children()}
				</main>
			</SmoothScrollBar>
		</div>
		<Toaster />
	</div>
{/if}

<style>
	.container {
		width: 100%;
		padding: 0;
		margin: 0;
		max-width: none;
	}
	.wappper {
		background: url('/img/bg.png');
	}

	.canva {
		position: absolute;
		width: 100vw;
		height: 100vh;
	}
</style>
