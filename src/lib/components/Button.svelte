<script lang="ts">
	let { text, action } = $props();
</script>

<button
	onclick={action}
	class="whitespace-nowrap rounded-md h-fit bg-white border-gray-300 border py-2.5 px-5 font-geist text-gray-800 text-xs cursor-pointer hover:bg-gray-300"
>
	{text}
</button>
