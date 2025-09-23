<script lang="ts">
	// Circular plates you can click + counter
	let { weight, color, count, minSize, addPlate } = $props();
	let total: number = $derived(count);
	$inspect(total);
	console.log('got here');
	$inspect(count);
</script>

<div class="flex flex-col items-center">
	<button
		onclick={addPlate}
		class={`rounded-full cursor-pointer ${color} aspect-square text-center hover:opacity-90 hover:scale-103 transition-opacity ease-out duration-75`}
		style="min-width: {minSize + 'px'}; min-height: {minSize + 'px'}"
	>
		<span class="font-geist text-sm font-semibold text-white">{weight}</span>
	</button>
	<div class="leading-none text-center w-fit h-fit rounded-sm bg-stone-100 px-1.5 py-0.5 mt-2">
		<span class="font-geist text-xs font-medium">
			{count}
		</span>
	</div>
</div>
