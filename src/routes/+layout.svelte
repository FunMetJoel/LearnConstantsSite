<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head><link rel="icon" href={favicon} /></svelte:head>

<div class="min-h-screen bg-slate-50 text-slate-900 dark:bg-slate-950 dark:text-slate-100 transition-colors duration-300">
  
  <main class="max-w-4xl mx-auto px-6 py-12 flex flex-col items-center justify-center">
    {@render children()}
  </main>
  
</div>
