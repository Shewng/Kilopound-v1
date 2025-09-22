<script lang="ts">
	import LandingPage from '$lib/components/LandingPage.svelte';
	import Footer from '$lib/components/Footer.svelte';
	import Button from '$lib/components/Button.svelte';
	import Plate from '$lib/components/Plate.svelte';

	interface BarbellType {
		name: string;
		kg: number;
		lbs: number;
	}

	// Plates needed for plate array and barbell
	interface Plate {
		weight: number;
		color: string;
		count: number;
	}

	const Unit = {
		kg: 'kg',
		lbs: 'lbs'
	};

	const BARBELL_TYPES: BarbellType[] = [
		{ name: 'Normal Barbell', kg: 20, lbs: 45 },
		{ name: 'Hex Trap Bar', kg: 25, lbs: 55 },
		{ name: 'Safety Squat Bar', kg: 25, lbs: 55 },
		{ name: 'EZ Curl Bar', kg: 7.5, lbs: 15 },
		{ name: 'Swiss/Football Bar', kg: 15, lbs: 35 },
		{ name: 'Log Bar', kg: 60, lbs: 135 },
		{ name: 'Axle Barbell', kg: 10, lbs: 25 },
		{ name: 'Cambered Squat Bar', kg: 40, lbs: 85 }
	];

	const PLATE_KG: Plate[] = [
		{ weight: 25, color: 'bg-red-700', count: 0 },
		{ weight: 20, color: 'bg-blue-800', count: 0 },
		{ weight: 15, color: 'bg-yellow-300', count: 0 },
		{ weight: 10, color: 'bg-green-600', count: 0 },
		{ weight: 5, color: 'bg-stone-300', count: 0 },
		{ weight: 2.5, color: 'bg-neutral-700', count: 0 },
		{ weight: 1.25, color: 'bg-zinc-500', count: 0 }
	];

	const PLATE_LBS: Plate[] = [
		{ weight: 45, color: 'bg-blue-500', count: 0 },
		{ weight: 35, color: 'bg-yellow-500', count: 0 },
		{ weight: 25, color: 'bg-green-500', count: 0 },
		{ weight: 10, color: 'bg-gray-500', count: 0 },
		{ weight: 5, color: 'bg-orange-500', count: 0 },
		{ weight: 2.5, color: 'bg-purple-500', count: 0 }
	];

	let plates: Plate[] = $state([]);
	let unit = $state(Unit.kg);

	let landingVisible: boolean = $state(false);
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
			<Button text={'Normal Barbell'} />

			<!-- Barbell -->
			<div></div>

			<!-- Plates -->

			<section class="flex justify-center items-end gap-2">
				<Plate weight={25} color={'bg-red-700'} size={'size-30'} />
				<Plate weight={20} color={'bg-blue-800'} size={'size-28'} />
				<Plate weight={15} color={'bg-yellow-300'} size={'size-26'} />
			</section>
			<section class="flex justify-center items-end gap-2 mt-5">
				<Plate weight={10} color={'bg-green-600'} size={'size-24'} />
				<Plate weight={5} color={'bg-stone-300'} size={'size-22'} />
				<Plate weight={2.5} color={'bg-neutral-700'} size={'size-20'} />
				<Plate weight={1.25} color={'bg-zinc-500'} size={'size-18'} />
			</section>

			<!-- Buttons -->
			<div class="flex flex-col justify-center items-center">
				<div class="flex gap-1">
					<Button text={'Swap to LBS'} />
					<Button text={'Reset'} />
					<Button text={'Normal Barbell'} />
				</div>
			</div>
		</div>
		<Footer />
	{/if}
</main>

<style>
	@reference "tailwindcss";
</style>
