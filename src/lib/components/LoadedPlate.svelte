<script>
	// Plates on the barbell
	// Rectangular plates you can click to remove
	let { weight, color, size, formatLoadedPlateNumber, removePlate } = $props();
	let width = $derived(size.width + 'px');
	let height = $derived(size.height + 'px');
</script>

<button
	onclick={removePlate}
	class={`cursor-pointer whitespace-pre-line rounded-sm mr-0.5 ${color} font-geist text-xs text-white font-medium`}
	style="min-width: {width}; min-height: {height}"
>
	{formatLoadedPlateNumber(weight)}
</button>
