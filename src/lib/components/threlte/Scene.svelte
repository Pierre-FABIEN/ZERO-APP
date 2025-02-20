<script>
	import { T } from '@threlte/core';
	import { ContactShadows, Float, Grid, OrbitControls } from '@threlte/extras';
	import { useGltf } from '@threlte/extras';

	let { fallback, error, children, ref = $bindable(), ...props } = $props();

	const gltf = useGltf('model/skull.glb');
</script>

<T.PerspectiveCamera makeDefault position={[-4, 0, 0]}>
	<OrbitControls autoRotate />
</T.PerspectiveCamera>

<T.DirectionalLight position={5} intensity={5} />

<T.Group bind:ref dispose={false} {...props}>
	{#await gltf}
		{@render fallback?.()}
	{:then gltf}
		<T.Mesh
			geometry={gltf.nodes.SM_Skull.geometry}
			material={gltf.materials['Material #25']}
			rotation={[-Math.PI / 2, 0, -Math.PI]}
		/>
	{:catch err}
		{@render error?.({ error: err })}
	{/await}

	{@render children?.({ ref })}
</T.Group>
