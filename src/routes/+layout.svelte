<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';
	import '../app.css';
	import '../font.css';
	import { dev } from '$app/environment';
	import { injectAnalytics } from '@vercel/analytics/sveltekit';

	injectAnalytics({ mode: dev ? 'development' : 'production' });

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<!-- this is a slot -->
{@render children?.()}
