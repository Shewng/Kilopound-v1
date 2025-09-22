<script>
	// Plates on the barbell
	// Circular plates you can click + counter
	let { weight, color, size, removePlate } = $props();
</script>

<button onclick={removePlate}>
	<div class="w-2 h-5 bg-red-500">{weight}</div>
</button>
