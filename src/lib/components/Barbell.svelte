<script lang="ts">
	import LoadedPlate from './LoadedPlate.svelte';

	// barbell is given an array of plates to display
	// depending on the unit (which is injected into props), toggle one or the other.
	/// if unit is kg, show kg weight, if unit is lbs, show lbs weight
	let { plates, unit } = $props();
</script>

<div>
	<img alt="" />
</div>
