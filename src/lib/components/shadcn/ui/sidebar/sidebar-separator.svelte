<script lang="ts">
	import { Separator } from '$components/shadcn/ui/separator/index.js';
	import { cn } from '$components/shadcn/utils.js';
	import type { ComponentProps } from 'svelte';

	let {
		ref = $bindable(null),
		class: className,
		...restProps
	}: ComponentProps<typeof Separator> = $props();
</script>

<Separator
	bind:ref
	data-sidebar="separator"
	class={cn('mx-2 w-auto bg-sidebar-border', className)}
	{...restProps}
/>
