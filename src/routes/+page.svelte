<script lang="ts">
	import LandingPage from '$lib/components/LandingPage.svelte';
	import Footer from '$lib/components/Footer.svelte';
	import Button from '$lib/components/Button.svelte';
	import Plate from '$lib/components/Plate.svelte';
	import LoadedPlate from '$lib/components/LoadedPlate.svelte';
	import Barbell from '$lib/components/Barbell.svelte';

	/* Interfaces */
	// Barbell has a name, and two unit values
	interface Barbell {
		name: string;
		kg: number;
		lbs: number;
	}

	// Plates needed for plate array and barbell
	interface Plate {
		weight: number;
		color: string;
		size: string;
		count: number;
	}

	// maybe change the limits, but just generic limiters
	const plateLimit: number = 10;
	const kgLimit: number = 600;
	const lbsLimit: number = 1250;

	const Unit = {
		kg: 'kg',
		lbs: 'lbs'
	};

	const BARBELL_TYPES: Barbell[] = [
		{ name: 'Normal Barbell', kg: 20, lbs: 45 },
		{ name: 'Hex Trap Bar', kg: 25, lbs: 55 },
		{ name: 'Safety Squat Bar', kg: 25, lbs: 55 },
		{ name: 'EZ Curl Bar', kg: 7.5, lbs: 15 },
		{ name: 'Swiss/Football Bar', kg: 15, lbs: 35 },
		{ name: 'Log Bar', kg: 60, lbs: 135 },
		{ name: 'Axle Barbell', kg: 10, lbs: 25 },
		{ name: 'Cambered Squat Bar', kg: 40, lbs: 85 }
	];

	// going to need to create a calulate function for the plate size. BOTH loaded and normal
	// change the size variable to maybe width and height, and calculate a 'size' for tailwind.
	// Loaded plates are vertical rectangles; plates are circles
	const PLATE_KG: Plate[] = [
		{ weight: 25, color: 'bg-red-700', size: 'size-30', count: 0 },
		{ weight: 20, color: 'bg-blue-800', size: 'size-28', count: 0 },
		{ weight: 15, color: 'bg-yellow-300', size: 'size-26', count: 0 },
		{ weight: 10, color: 'bg-green-600', size: 'size-24', count: 0 },
		{ weight: 5, color: 'bg-stone-300', size: 'size-22', count: 0 },
		{ weight: 2.5, color: 'bg-neutral-700', size: 'size-20', count: 0 },
		{ weight: 1.25, color: 'bg-zinc-500', size: 'size-18', count: 0 }
	];
	// going to need to create a calulate function for the plate size. BOTH loaded and normal
	// change the size variable to maybe width and height, and calculate a 'size' for tailwind.
	// Loaded plates are vertical rectangles; plates are circles
	const PLATE_LBS: Plate[] = [
		{ weight: 45, color: 'bg-gray-900', size: 'size-30', count: 0 },
		{ weight: 35, color: 'bg-gray-900', size: 'size-28', count: 0 },
		{ weight: 25, color: 'bg-gray-900', size: 'size-26', count: 0 },
		{ weight: 10, color: 'bg-gray-900', size: 'size-24', count: 0 },
		{ weight: 5, color: 'bg-gray-900', size: 'size-20', count: 0 },
		{ weight: 2.5, color: 'bg-gray-900', size: 'size-18', count: 0 }
	];

	/* States */
	let unit = $state(Unit.kg);
	let barbell: Barbell = $state(BARBELL_TYPES[0]);
	let plates: Plate[] = $derived([]);
	let total: number = $derived(
		unit == Unit.kg
			? plates.reduce((sum, plate) => sum + plate.weight * plate.count, 0) + barbell.kg
			: plates.reduce((sum, plate) => sum + plate.weight * plate.count, 0) + barbell.lbs
	);
	let convertedTotal = $derived(
		unit == Unit.kg ? (total * 2.2046).toFixed(1) : (total / 2.2046).toFixed(1)
	);

	// landing
	let landingVisible: boolean = $state(false);

	// input mode setup
	let isInputMode: boolean = $state(false);
	let isInputLoading: boolean = $state(false);
	let isErrorInput: boolean = $state(false);

	$inspect(plates);

	/* Functions */
	// Add plate to the array, visually to the barbell
	// recalculate weight
	// increase plate counter's total
	function addPlate(plate: Plate) {
		plates = [...plates, plate];
		//plates = plates.map((current) =>
		//	current.weight === plate.weight ? { ...plate, count: plate.count + 2 } : plate
		//);
		sortLoadedPlates();
	}

	function toggleUnit() {
		if (unit === Unit.kg) unit = Unit.lbs;
		else unit = Unit.kg;
		resetBarbell();
	}

	function resetBarbell() {
		plates = [];
	}

	// everytime you add a plate, call this.
	function sortLoadedPlates() {}

	function calculatePlateSize() {} // calculate loaded plates and plates sizes
	function openManual() {}
</script>

<main class="relative">
	{#if landingVisible}
		<LandingPage
			dismiss={() => {
				landingVisible = false;
			}}
		/>
	{:else}
		<div class="mx-4 my-2">
			<!-- Title -->
			<h1 class="font-libre italic text-gray-800 text-3xl/normal lg:text-6xl/normal">Kilopound</h1>
			<h2 class="font-libre text-gray-800 text-xs lg:text-base">visualize weights on a barbell</h2>
			<Button text={'manual'} action={openManual} />

			<!-- Total Weight Display -->
			<div class="flex flex-col items-center text-center">
				<span>{barbell.name}</span>
				<h2>Total Weight: {total}</h2>
				<span>{convertedTotal} {unit}</span>
			</div>
			<!-- Barbell -->
			<div class="flex items-center justify-center min-h-50">
				<div>Map loaded plates here</div>
				<div>Put barbell component here</div>
				<div>Map loaded plates here</div>
			</div>

			<!-- Plates -->
			{#if unit == Unit.kg}
				<section class="flex justify-center flex-wrap items-end gap-2">
					{#each PLATE_KG as plate, i}
						<Plate {...plate} addPlate={() => addPlate(plate)} />
					{/each}
				</section>
			{:else if unit == Unit.lbs}
				<section class="flex justify-center flex-wrap items-end gap-2">
					{#each PLATE_LBS as plate}
						<Plate {...plate} addPlate={() => addPlate(plate)} />
					{/each}
				</section>
			{/if}

			<!-- Buttons -->
			<div class="flex flex-col justify-center items-center">
				<div class="flex gap-1">
					<Button text={'Swap to LBS'} action={toggleUnit} />
					<Button text={'Reset'} action={resetBarbell} />
					<Button text={'Normal Barbell'} action={toggleUnit} />
				</div>
			</div>
		</div>
		<Footer />
	{/if}
</main>

<style>
	@reference "tailwindcss";
</style>
