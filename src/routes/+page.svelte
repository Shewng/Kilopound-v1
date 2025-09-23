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

	/* Constants */
	const Unit = {
		kg: 'KG',
		lbs: 'LBS'
	};

	const PLATE_LIMIT = 20;
	const WEIGHT_LIMIT_KG = 600;
	const WEIGHT_LIMIT_LBS = 1250;

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

	/* States */
	const PLATE_KG: Plate[] = $state([
		{ weight: 25, color: 'bg-red-700', size: 'size-30', count: 0 },
		{ weight: 20, color: 'bg-blue-800', size: 'size-28', count: 0 },
		{ weight: 15, color: 'bg-yellow-300', size: 'size-26', count: 0 },
		{ weight: 10, color: 'bg-green-600', size: 'size-24', count: 0 },
		{ weight: 5, color: 'bg-stone-300', size: 'size-22', count: 0 },
		{ weight: 2.5, color: 'bg-neutral-700', size: 'size-20', count: 0 },
		{ weight: 1.25, color: 'bg-zinc-500', size: 'size-18', count: 0 }
	]);

	const PLATE_LBS: Plate[] = $state([
		{ weight: 45, color: 'bg-gray-900', size: 'size-30', count: 0 },
		{ weight: 35, color: 'bg-gray-900', size: 'size-28', count: 0 },
		{ weight: 25, color: 'bg-gray-900', size: 'size-26', count: 0 },
		{ weight: 10, color: 'bg-gray-900', size: 'size-24', count: 0 },
		{ weight: 5, color: 'bg-gray-900', size: 'size-20', count: 0 },
		{ weight: 2.5, color: 'bg-gray-900', size: 'size-18', count: 0 }
	]);

	let barbell: Barbell = $state(BARBELL_TYPES[0]);
	let plates: Plate[] = $derived([]);
	let renderPlates: Plate[] = $derived(renderLoadedPlates(plates));
	let unit: string = $state(Unit.kg);
	let convertedUnit: string = $derived(unit === Unit.kg ? Unit.lbs : Unit.kg);
	let total: number = $derived(
		unit == Unit.kg
			? plates.reduce((sum, plate) => sum + plate.weight * plate.count, 0) + barbell.kg
			: plates.reduce((sum, plate) => sum + plate.weight * plate.count, 0) + barbell.lbs
	);
	let convertedTotal: number = $derived(
		unit == Unit.kg
			? parseFloat((total * 2.2046).toFixed(0))
			: parseFloat((total / 2.2046).toFixed(0))
	);

	// Booleans for visibility
	let landingVisible: boolean = $state(false);
	let manualVisible: boolean = $state(false);

	// Booleans for limits; true = reached, false = havent reached
	const isPlateLimit: boolean = $derived(
		plates.reduce((sum, plate) => sum + plate.count, 0) >= PLATE_LIMIT
	);
	const isWeightLimit: boolean = $derived(
		unit == Unit.kg
			? plates.reduce((sum, plate) => sum + plate.count, 0) >= WEIGHT_LIMIT_KG
			: plates.reduce((sum, plate) => sum + plate.count, 0) >= WEIGHT_LIMIT_LBS
	);

	// Input mode setup
	let isInputMode: boolean = $state(false);
	let isInputLoading: boolean = $state(false);
	let isErrorInput: boolean = $state(false);

	$inspect(plates);

	/* Functions */
	// Add plate pair to the plates array
	function addPlate(plate: Plate) {
		if (!isPlateLimit) {
			if (plates.some((p) => p.weight === plate.weight)) {
				plates = plates.map((current) =>
					current.weight === plate.weight ? { ...current, count: current.count + 2 } : current
				);
			} else {
				plates = [...plates, { ...plate, count: 2 }];
			}
			sortLoadedPlates();
		} else {
			// Update error handling messaging here
		}
	}

	// Remove loaded plate pair from barbell
	function removePlate(plate: Plate) {
		plates = plates
			.map((current) =>
				current.weight === plate.weight ? { ...current, count: current.count - 2 } : current
			)
			.filter((current) => current.count > 0);
		sortLoadedPlates();
	}

	// Halves the current plate array's 'count' to render one side of barbell's plates
	function renderLoadedPlates(plates: Plate[]) {
		return plates.flatMap((plate) => {
			const half = Math.floor(plate.count / 2);
			return Array(half).fill(plate);
		});
	}

	// Toggle between KG and LBS
	function toggleUnit() {
		if (unit === Unit.kg) unit = Unit.lbs;
		else unit = Unit.kg;
		resetBarbell();
	}

	// Sort loaded plates
	function sortLoadedPlates() {
		plates = [...plates].sort((a, b) => b.weight - a.weight);
	}

	// Format loaded plates' weight text to be vertical, using newlines
	function formatLoadedPlateNumber(weight: number) {
		const weightStr = weight.toString();
		return weightStr.split('').join('\n');
	}

	// Calculate loaded plates' sizes
	function calculateLoadedPlateSize(weight: number) {
		const height = 50 + weight * 6;
		const width = 10 + weight * 0.35;

		return { width, height };
	}

	// Calculate round plates' sizes
	function calculatePlateSize(weight: number) {
		if (unit === Unit.kg) {
			return 50 + weight * 3;
		} else {
			return 40 + weight * 2;
		}
	}

	// Reset Barbell UI and Total Weight Display by clearing plates array
	function resetBarbell() {
		plates = [];
	}

	// Open manual
	function openManual() {
		manualVisible = true;
	}
</script>

<main class="relative">
	{#if landingVisible}
		<LandingPage
			dismiss={() => {
				landingVisible = false;
			}}
		/>
	{:else}
		<div class="relative flex flex-col items-center mx-4 mt-2 h-screen">
			<!-- Title -->
			<div class="flex justify-between w-full mb-15">
				<div class="flex flex-col">
					<h1 class="font-libre italic text-slate-800 text-3xl/normal lg:text-6xl/normal">
						Kilopound
					</h1>
					<h2 class="font-libre text-slate-800 text-xs lg:text-base">
						visualize weights on a barbell
					</h2>
				</div>
				<Button text={'manual'} action={openManual} />
			</div>

			<!-- Total Weight Display -->
			<div class="flex flex-col items-center text-center">
				<span class="text-xs text-gray-400 z-10">{barbell.name}</span>
				<h2 class="font-geist font-bold text-3xl text-slate-800 py-2.5 z-10">{total} {unit}</h2>
				<span class="font-geist text-lg text-gray-400 z-10">{convertedTotal} {convertedUnit}</span>
				<div class="relative text-center flex flex-col items-center z-5 overflow-clip">
					<h2 class="font-geist font-semibold text-9xl text-stone-50 absolute bottom-0 opacity-98">
						{total}{unit}
					</h2>
				</div>
			</div>
			<!-- Barbell -->
			<div class=" overflow-clip">
				<section class="hidden sm:flex">
					<div>Map loaded plates here</div>
					<div class="w-70 h-2 bg-gray-800"></div>
					<div>Map loaded plates here</div>
				</section>
				{#if unit == Unit.kg}
					<section class="flex items-center justify-center min-h-50 sm:hidden my-12">
						<div class="w-30 h-3 bg-gray-800"></div>
						{#each renderPlates as plate}
							<LoadedPlate
								{...plate}
								size={calculateLoadedPlateSize(plate.weight)}
								formatLoadedPlateNumber={() => formatLoadedPlateNumber(plate.weight)}
								removePlate={() => removePlate(plate)}
							/>
						{/each}
					</section>
				{/if}
			</div>

			<!-- Plates -->
			<section class="min-h-100">
				{#if unit == Unit.kg}
					<section class="flex justify-center w-full items-end gap-2">
						{#each PLATE_KG.slice(0, 3) as plate, i}
							<Plate
								{...plate}
								minSize={calculatePlateSize(plate.weight)}
								addPlate={() => {
									(addPlate(plate), (plates = plates));
								}}
							/>
						{/each}
					</section>
					<section class="flex justify-center w-full items-end gap-2 mt-5">
						{#each PLATE_KG.slice(3) as plate, i}
							<Plate
								weight={plate.weight}
								color={plate.color}
								minSize={calculatePlateSize(plate.weight)}
								count={plate.count}
								addPlate={() => addPlate(plate)}
							/>
						{/each}
					</section>
				{:else if unit == Unit.lbs}
					<section class="flex justify-center flex-wrap items-end gap-2">
						{#each PLATE_LBS.slice(0, 3) as plate}
							<Plate
								weight={plate.weight}
								color={plate.color}
								minSize={calculatePlateSize(plate.weight)}
								count={plate.count}
								addPlate={() => addPlate(plate)}
							/>
						{/each}
					</section>
					<section class="flex justify-center flex-wrap items-end gap-2 mt-5">
						{#each PLATE_LBS.slice(3) as plate}
							<Plate
								weight={plate.weight}
								color={plate.color}
								minSize={calculatePlateSize(plate.weight)}
								count={plate.count}
								addPlate={() => addPlate(plate)}
							/>
						{/each}
					</section>
				{/if}
			</section>

			<!-- Buttons -->
			<div class="fixed bottom-0 flex flex-col justify-center items-center mt-auto mb-5">
				<div class="flex gap-1">
					<Button text={'Swap to LBS'} action={toggleUnit} />
					<Button text={'Reset'} action={resetBarbell} />
					<Button text={'Normal Barbell'} action={toggleUnit} />
				</div>
			</div>
		</div>
		<!--
			<div class="relative h-[44px]">
				<Footer />
			</div>
		-->
	{/if}
</main>

<style>
	@reference "tailwindcss";
</style>
