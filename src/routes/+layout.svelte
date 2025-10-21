<script>
	import '../app.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();

	// ✅ Register Service Worker for PWA
	if ('serviceWorker' in navigator) {
		window.addEventListener('load', () => {
			navigator.serviceWorker
				.register('/service-worker.js')
				.then(() => console.log('✅ Service Worker registered'))
				.catch((err) => console.error('Service Worker registration failed:', err));
		});
	}
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
	<!-- ✅ Add PWA Manifest -->
	<link rel="manifest" href="/manifest.json" />
	<meta name="theme-color" content="#f9a8d4" />
	<meta name="viewport" content="width=device-width, initial-scale=1" />
	<meta name="description" content="A Kanban Board PWA that works offline!" />
</svelte:head>

{@render children?.()}
