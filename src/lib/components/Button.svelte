<script lang="ts">
	let { text, action, icon = null } = $props();
</script>

<button
	onclick={action}
	class="whitespace-nowrap rounded-md h-fit bg-white border-gray-300 border py-2.5 px-5 font-geist text-gray-800 text-xs cursor-pointer hover:bg-gray-300"
>
	{#if icon}
		<span class="flex items-center">
			{text}<span class={`${icon} ml-1`}></span>
		</span>
	{:else}
		{text}
	{/if}
</button>
