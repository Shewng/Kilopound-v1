<script lang="ts">
	// Circular plates you can click + counter
	let { weight, color, size, count = 0, addPlate } = $props();
	let total: number = $derived(count); // not sure if this is correct
</script>

<div class="flex flex-col items-center">
	<button
		onclick={addPlate}
		class={`rounded-full cursor-pointer ${color} ${size} hover:opacity-90 hover:scale-101 transition-all ease-out duration-75`}
	>
		<span class="font-geist text-sm font-semibold text-white">{weight}</span>
	</button>
	<div class="leading-none text-center w-fit h-fit rounded-sm bg-stone-100 px-1.5 py-0.5 mt-2">
		<span class="font-geist text-xs font-semibold">
			{total}
		</span>
	</div>
</div>
