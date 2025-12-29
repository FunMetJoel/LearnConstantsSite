<script>
    let count = $state(0);


</script>

<section class="w-full space-y-8 text-center">
  <header class="space-y-4">
    <h1 class="text-4xl md:text-6xl font-extrabold tracking-tight">
      SvelteKit + <span class="text-sky-500">Tailwind</span>
    </h1>
    <p class="text-lg text-slate-600 dark:text-slate-400 max-w-lg mx-auto">
      This is a centered, dark-mode ready layout that scales beautifully across devices.
    </p>
  </header>

  <div class="grid gap-4 sm:grid-cols-2 w-full">
    <div class="p-6 bg-white dark:bg-slate-900 border border-slate-200 dark:border-slate-800 rounded-2xl shadow-sm">
      <h3 class="font-bold">Feature One</h3>
      <p class="text-sm opacity-80">Clean cards with subtle borders look great in dark mode.</p>
    </div>
    <div class="p-6 bg-white dark:bg-slate-900 border border-slate-200 dark:border-slate-800 rounded-2xl shadow-sm">
      <h3 class="font-bold">Feature Two</h3>
      <p class="text-sm opacity-80">Using rounded-2xl gives a modern, friendly feel.</p>
    </div>
  </div>

  <button onclick={() => count++} class="px-6 py-3 bg-sky-600 hover:bg-sky-500 text-white font-medium rounded-full transition-all">
    {count}
  </button>
</section>