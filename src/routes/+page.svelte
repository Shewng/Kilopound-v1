<script lang="ts">
	import LandingPage from '$lib/components/LandingPage.svelte';
	import Footer from '$lib/components/Footer.svelte';
	import Button from '$lib/components/Button.svelte';
	import Plate from '$lib/components/Plate.svelte';
	import LoadedPlate from '$lib/components/LoadedPlate.svelte';
	import Barbell from '$lib/components/Barbell.svelte';
	import Manual from '$lib/components/Manual.svelte';
	import { tick } from 'svelte';

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
	let renderPlatesReverse: Plate[] = $derived(renderPlates.reverse());
	let unit: string = $state(Unit.lbs);
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
	let inputTotal: number = $derived(total);
	let inputTotalMod: number = $derived(inputTotal);

	// Booleans for limits; true = reached, false = havent reached
	const isPlateLimit: boolean = $derived(
		plates.reduce((sum, plate) => sum + plate.count, 0) >= PLATE_LIMIT
	);
	const isWeightLimit: boolean = $derived(
		unit == Unit.kg
			? plates.reduce((sum, plate) => sum + plate.count, 0) >= WEIGHT_LIMIT_KG
			: plates.reduce((sum, plate) => sum + plate.count, 0) >= WEIGHT_LIMIT_LBS
	);

	let isError: boolean = $state(false); // generic error (non-input)

	// Manual mode setup
	let isManualMode: boolean = $state(false);
	let isWalkthroughMode: boolean = $state(false);

	// Input mode setup
	let isInputMode: boolean = $state(false);
	let isInputLoading: boolean = $state(false);
	let isInputError: boolean = $state(false); // input error
	let inputRef: HTMLInputElement | null = $state(null);

	// Booleans for visibility
	let landingVisible: boolean = $state(true);
	let overlayVisible: boolean = $derived(isManualMode || isInputMode);

	$effect(() => {
		overlayVisible
			? document.body.classList.add('overflow-hidden')
			: document.body.classList.remove('overflow-hidden');
	});

	/* Functions */
	// Add plate pair to the plates array
	function addPlate(plate: Plate) {
		if (!isPlateLimit) {
			if (plates.some((p) => p.weight === plate.weight)) {
				plates = plates.map((current, i) =>
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

	// Every time we add a plate, update individual plate counter
	function updatePlateCount(plate: Plate) {
		return plates.find((p) => p.weight === plate.weight)?.count;
	}

	// Halves the current plate array's 'count' to render one side of barbell's plates
	function renderLoadedPlates(plates: Plate[]) {
		console.log(plates);
		return plates.flatMap((plate) => {
			const half = Math.floor(plate.count / 2);
			return Array(half).fill(plate);
		});
	}

	// Sort loaded plates
	function sortLoadedPlates() {
		plates = [...plates].sort((a, b) => a.weight - b.weight);
	}

	// Format loaded plates' weight text to be vertical, using newlines
	function formatLoadedPlateNumber(weight: number) {
		const weightStr = weight.toString();
		return weightStr.split('').join('\n');
	}

	// Calculate loaded plates' sizes
	function calculateLoadedPlateSize(weight: number) {
		let height: number;
		let width: number;

		if (unit == Unit.kg) {
			height = 55 + weight * 4;
			width = 10 + weight * 0.35;
		} else {
			height = 55 + weight * 2;
			width = 10 + weight * 0.25;
		}

		return { width, height };
	}

	// Calculate round plates' sizes
	function calculatePlateSize(weight: number) {
		if (unit === Unit.kg) {
			return 50 + weight * 2.8;
		} else {
			return 50 + weight * 1.3;
		}
	}

	// Check if loaded plate is hovered
	//function handleLoadedPlateHover(position: number) {
	//	let flag = false;
	//	console.log(position);
	//	console.log('got inhere');
	//	// if plate is hovered at position, toggle it true in the array.
	//	renderPlates.forEach((plate1) => {
	//		console.log('plate1: ' + plate1.position);
	//		renderPlatesReverse.forEach((plate2) => {
	//			console.log('plate2: ' + plate2.position);
	//			if (plate1.position === position && plate2.position === position) {
	//				console.log('got inside');
	//
	//				//plate2.isHovered = true;
	//			}
	//		});
	//	});
	//	console.log(renderPlates);
	//	console.log(renderPlatesReverse);
	//}

	// Toggle between KG and LBS
	function toggleUnit() {
		if (unit === Unit.kg) unit = Unit.lbs;
		else unit = Unit.kg;
		resetBarbell();
	}

	// Reset Barbell UI and Total Weight Display by clearing plates array
	function resetBarbell() {
		plates = [];
	}

	// Open input mode
	function openInput() {
		isInputMode = true;

		tick().then(() => {
			// highlight input for UX
			if (inputRef) {
				inputRef.focus();
				inputRef.select();
			}
		});
	}

	// Close input mode
	function closeOverlay() {
		inputTotal = total; // match input total to total
		isManualMode = false;
		isInputMode = false;
		isInputError = false;
	}

	// Prevent certain characters from being entered into the input
	function validateInput(e: any) {
		let input;
		if (inputTotal == null) input = '0';
		else input = inputTotal.toString();

		// check user paste value
		if (e.data && e.inputType == 'insertFromPaste') {
			if (input.includes('.')) {
				// if input already includes decimal, user can only paste numbers.
				if (!/^\d+$/.test(e.data)) e.preventDefault();
			} else {
				// if input doesn't include a decimal, user can only paste numbers and at most 1 decimal.
				if (!/^\d*\.?\d*$/.test(e.data)) e.preventDefault();
			}
		}
		// user can only input one decimal
		else if (e.data && e.data == '.') {
			// block any more decimal inputs
			if (input.includes('.')) e.preventDefault();
		}
		// user can only input numerical characters
		else if (e.data && !/^\d$/.test(e.data)) {
			e.preventDefault();
		}
	}

	// load calculated weight
	function loadWeight() {
		// study V0 algorithm
		// take input weight and reduce number to 2 decimal places
		// check if input weight is too big or small, toggle isInputError
		// grab plate values, from largest to smallest
		// for each plate, find the remainders using plate value
		// divide total plate values by 2. if not divisible by 2, move to the next possible weight value
		// continue until left no more remainder. round this number to the closest plate value
		// trigger boolean if rounded, to show message
		// add plates into array, updating total.
		// close input modal

		isInputError = true;
		// intentionally trigger loading state
		//closeInput();
	}

	// Open manual
	function openManual() {
		isManualMode = true;
	}

	// Begin walkthrough process
	function openWalkthrough() {
		isWalkthroughMode = true;
	}

	// Open barbell options menu
	function openBarbellOptions() {}
</script>

<main class="relative touch-manipulation">
	{#if landingVisible}
		<LandingPage
			dismiss={() => {
				landingVisible = false;
			}}
		/>
	{:else}
		<!-- Blur overlay -->
		{#if isInputMode || isManualMode}
			<!-- svelte-ignore a11y_click_events_have_key_events -->
			<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
			<div
				onclick={closeOverlay}
				role="region"
				class="fixed inset-0 h-screen w-full backdrop-blur-md z-11 cursor"
			></div>
		{/if}
		<div class="relative flex flex-col items-center px-4 pt-2">
			<!-- Title -->
			<div class="flex justify-between w-full mb-10">
				<div class="flex flex-col">
					<h1 class="font-libre italic text-slate-800 text-3xl/normal lg:text-6xl/normal">
						Kilopound
					</h1>
					<h2 class="font-libre text-slate-800 text-xs lg:text-base">
						visualize weights on a barbell
					</h2>
				</div>
				<Button text={'manual'} action={openManual} />
				{#if isManualMode}
					<Manual {closeOverlay} {openWalkthrough} />
				{/if}
			</div>

			<!-- Total Weight Display -->
			<div class="flex flex-col items-center text-center">
				<span class="text-xs text-gray-400 z-10">{barbell.name}</span>
				<div class="relative flex items-center justify-center">
					<button
						onclick={openInput}
						class="font-geist font-bold text-center text-3xl text-slate-800 px-3.5 py-1.75 w-full border border-transparent hover:rounded-md hover:border-1 hover:border-gray-300 cursor-pointer z-10"
						>{total}
						{unit}
						<span
							class="self-center absolute bottom-1/2 top-1/2 mb-0.5 ml-4 icon-[material-symbols--edit-outline] text-lg"
						></span></button
					>
					<!-- Input Mode -->
					{#if isInputMode}
						<!-- Mobile -->
						<div
							class="absolute top-[-31px] sm:top-auto flex flex-col sm:flex-row w-full justify-start items-center z-11"
						>
							<div
								class="flex flex-col sm:absolute sm:right-[105%] sm:text-right whitespace-nowrap"
							>
								{#if unit === Unit.kg}
									<p class="font-geist text-xs text-gray-400">
										Max load: {WEIGHT_LIMIT_KG}
										{unit}
									</p>
								{/if}
								{#if unit === Unit.lbs}
									<p class="font-geist text-xs text-gray-400">
										Max load: {WEIGHT_LIMIT_LBS}
										{unit}
									</p>
								{/if}
								<p class="font-geist text-xs text-gray-400">Max plates per side: {PLATE_LIMIT}</p>
							</div>
							<div class="flex flex-col justify-start my-1 sm:m-0">
								<input
									type="text"
									inputmode="numeric"
									class={`${isInputError ? 'border-red-500' : 'border-gray-300'} border outline-none font-geist font-bold text-center px-3.5 py-1.25 w-full bg-white text-3xl text-slate-800 rounded-md [-moz-appearance:_textfield] [&::-webkit-inner-spin-button]:m-0 [&::-webkit-inner-spin-button]:appearance-none [&::-webkit-outer-spin-button]:m-0 [&::-webkit-outer-spin-button]:appearance-none`}
									bind:value={inputTotal}
									bind:this={inputRef}
									placeholder="0"
									onbeforeinput={(e) => validateInput(e)}
								/>
								<p
									class={`${isInputError ? 'visible' : 'invisible'} mt-1 mb-1 sm:mb-0 sm:absolute sm:left-auto sm:top-full font-geist text-xs text-red-500 whitespace-nowrap`}
								>
									Provide a valid number.
								</p>
							</div>
							<button
								onclick={loadWeight}
								class="font-geist bg-slate-800 text-white whitespace-nowrap sm:absolute sm:left-[105%] rounded-md w-full h-fit py-2.5 px-5 text-xs cursor-pointer"
								>Autoload</button
							>
						</div>
					{/if}
				</div>
				<span class="font-geist text-lg text-gray-400 z-10">{convertedTotal} {convertedUnit}</span>
				<div class="relative text-center flex flex-col items-center z-5 overflow-clip">
					<h2 class="font-geist font-semibold text-9xl text-stone-50 absolute bottom-0 opacity-98">
						{total}{unit}
					</h2>
				</div>
			</div>

			<!-- Barbell -->
			<div class="overflow-clip">
				<!-- Desktop -->
				<section class="hidden sm:flex items-center justify-center min-h-50 mt-4 mb-10">
					{#each renderPlates as plate}
						<LoadedPlate
							{...plate}
							size={calculateLoadedPlateSize(plate.weight)}
							formatLoadedPlateNumber={() => formatLoadedPlateNumber(plate.weight)}
							removePlate={() => removePlate(plate)}
						/>
					{/each}
					<div class="w-70 h-2 bg-gray-800"></div>
					{#each renderPlatesReverse as plate}
						<LoadedPlate
							{...plate}
							size={calculateLoadedPlateSize(plate.weight)}
							formatLoadedPlateNumber={() => formatLoadedPlateNumber(plate.weight)}
							removePlate={() => removePlate(plate)}
						/>
					{/each}
				</section>
				<!-- Mobile -->
				<section class="flex items-center justify-center min-h-45 my-4 sm:hidden">
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
			</div>

			<!-- Plates -->
			<section class="flex flex-col sm:flex-row sm:items-end sm:gap-2 items-center mb-22">
				{#if unit == Unit.kg}
					<section class="flex justify-center w-full items-end gap-2.5">
						{#each PLATE_KG.slice(0, 3) as plate, i}
							<Plate
								{...plate}
								minSize={calculatePlateSize(plate.weight)}
								count={updatePlateCount(plate)}
								addPlate={() => {
									(addPlate(plate), (plates = plates));
								}}
							/>
						{/each}
					</section>
					<section class="flex justify-center w-full items-end gap-2.5 mt-4 sm:mt-0">
						{#each PLATE_KG.slice(3) as plate, i}
							<Plate
								{...plate}
								minSize={calculatePlateSize(plate.weight)}
								count={updatePlateCount(plate)}
								addPlate={() => addPlate(plate)}
							/>
						{/each}
					</section>
				{:else if unit == Unit.lbs}
					<section class="flex justify-center w-full items-end gap-2.5">
						{#each PLATE_LBS.slice(0, 3) as plate}
							<Plate
								{...plate}
								minSize={calculatePlateSize(plate.weight)}
								count={updatePlateCount(plate)}
								addPlate={() => addPlate(plate)}
							/>
						{/each}
					</section>
					<section class="flex justify-center w-full items-end gap-2.5 mt-4 sm:mt-0">
						{#each PLATE_LBS.slice(3) as plate}
							<Plate
								{...plate}
								minSize={calculatePlateSize(plate.weight)}
								count={updatePlateCount(plate)}
								addPlate={() => addPlate(plate)}
							/>
						{/each}
					</section>
				{/if}
			</section>

			<!-- Buttons -->
			<div class=" bottom-0 flex flex-col justify-center items-center mt-auto mb-2">
				<div class="flex gap-1">
					<Button text={'Swap to LBS'} action={toggleUnit} />
					<Button text={'Reset'} action={resetBarbell} icon={'icon-[ri--reset-left-fill]'} />
					<Button text={'Normal Barbell'} action={openBarbellOptions} />
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
