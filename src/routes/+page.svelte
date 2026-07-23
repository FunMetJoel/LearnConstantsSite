<script lang="js">
  let multiplayer = $state(false);
</script>

<section class="w-full space-y-8 text-center">
  <header class="space-y-4">
    <h1 class="text-5xl md:text-6xl font-extrabold tracking-tight">
      SJIERD!
    </h1>
    <h1 class="text-4xl md:text-6xl font-extrabold tracking-tight">
      Leer nog meer <span class="text-sky-500">Random getallen</span>
    </h1>
    <p class="text-lg text-slate-600 dark:text-slate-400 max-w-lg mx-auto">
      Ik kan nog wel wat getallen toevoegen als je wilt.
    </p>
  </header>

  <div class="flex items-center justify-center gap-3">
    <label class="inline-flex items-center gap-2 text-slate-700 dark:text-slate-300">
      <input
        type="checkbox"
        bind:checked={multiplayer}
        class="h-5 w-5 rounded border-slate-300 text-sky-500 focus:ring-sky-500"
      />
      Multiplayer
    </label>
  </div>

  <div class="grid gap-4 sm:grid-cols-2 w-full">
    <a href={multiplayer ? "/challange/pi" : "/pi" } class="p-6 bg-white dark:bg-slate-900 border border-slate-200 dark:border-slate-800 rounded-2xl shadow-sm hover:border-slate-700 hover:drop-shadow-sky-900 drop-shadow-md">
      <h3 class="font-bold">π</h3>
      <p class="text-sm opacity-80">3.141592653589793238462643383...</p>
    </a>
    <a href={multiplayer ? "/challange/e" : "/e" } class="p-6 bg-white dark:bg-slate-900 border border-slate-200 dark:border-slate-800 rounded-2xl shadow-sm hover:border-slate-700 hover:drop-shadow-sky-900 drop-shadow-md">
      <h3 class="font-bold">e</h3>
      <p class="text-sm opacity-80">2.718281828459045235360287471...</p>
    </a>
  </div>
</section>