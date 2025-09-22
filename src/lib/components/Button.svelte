<script lang="ts">
	let { text, action } = $props();
</script>

<button
	onclick={action}
	class="rounded-md border-gray-300 border py-3 px-5 font-geist text-gray-800 text-xs"
>
	{text}
</button>
