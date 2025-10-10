<script lang="ts">
	import icon from '$lib/assets/iconplate.svg';
	let { closeOverlay, openWalkthrough } = $props();
</script>

<div
	class="fixed inset-0 flex flex-col py-10 p-8 w-90/100 max-w-xl h-fit m-auto bg-white ring-1 ring-gray-300 rounded-2xl z-11"
>
	<h2 class="flex font-libre text-2xl lg:text-3xl text-slate-800">
		<img src={icon} alt="" class="mr-2 w-11" />Manual
	</h2>
	<div class="mt-6 mb-8">
		<p class="font-geist text-sm text-slate-800">
			Kilopound is a tool that helps you <span class="font-semibold"
				>visualize weights on a barbell.</span
			>
		</p>
		<ul class="font-geist text-sm ml-2 text-slate-800 list-disc">
			<br />
			<p><span class="mr-1">•</span> Click the circular weight plates to load the barbell</p>
			<br />
			<p><span class="mr-1">•</span> Click a loaded plate on the barbell to remove it</p>
			<br />
			<p>
				<span class="mr-1">•</span> All plates on the barbell can be cleared using the reset button
			</p>
			<br />
			<p>
				<span class="mr-1">•</span> Manually enter a weight total to auto-load the barbell with plates
			</p>
			<br />
			<p><span class="mr-1">•</span> Convert between KG and LBS, and swap between units</p>
			<br />
			<p><span class="mr-1">•</span> Switch between different barbells for your use case</p>
		</ul>
	</div>
	<button
		onclick={closeOverlay}
		class="font-geist bg-slate-800 text-white whitespace-nowrap rounded-md w-full h-fit border py-2.5 px-5 text-sm cursor-pointer"
		>Walkthrough</button
	>
	<span class="font-libre italic text-center text-xs sm:text-sm mt-5 text-slate-500"
		>Click anywhere or press any key to close</span
	>
</div>
