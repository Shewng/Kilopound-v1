<script lang="ts">
	import Footer from './Footer.svelte';
	import landing from '$lib/assets/landingplate.svg';

	let { dismiss } = $props();
</script>

<svelte:window onkeydown={dismiss} />

<section
	class="cursor-pointer relative flex flex-col justify-center items-center min-h-screen h-screen"
	onclick={dismiss}
	role="presentation"
>
	<div class="text-center -translate-y-10 lg:-translate-y-10">
		<h1 class="font-libre italic text-6xl/normal lg:text-9xl/normal text-slate-800">Kilopound</h1>
		<p class="font-libre text-base lg:text-xl text-gray-700">visualizing weights on a barbell</p>
	</div>
	<p class="font-libre italic text-sm text-slate-500 fixed bottom-1/9">
		Click anywhere or press any key to start
	</p>
	<!--
        <h1 class="font-libre italic text-[14rem]/normal text-gray-800 fixed opacity-5 -translate-y-1/20">
            Kilopound
        </h1>
        -->
	<img class="fixed w-95/100 bottom-87 lg:bottom-50" src={landing} alt="" />
</section>
<Footer />

<style>
	@reference "tailwindcss";
</style>
