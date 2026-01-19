<script lang="ts">
    import { flip } from 'svelte/animate';
    import { fade } from 'svelte/transition';
    import { cubicOut } from 'svelte/easing';
    import type { PageData } from './$types';

    let currentCharacterIndex = $state(2);

    let { data } = $props<{ data: PageData }>();

    const characterString = data.characterString;

    // Static offsets for calculation logic
    const offsets = [-4, -3, -2, -1, 0, 1, 2, 3, 4];
    
    // Derived state: The actual indices of the characters we want to show.
    // We map these so we can key the block by the absolute index.
    let visibleIndices = $derived(offsets.map(o => currentCharacterIndex + o));

    let challangemode: boolean = $state(true);

    function getStyles(offset: number) {
        const absOffset = Math.abs(offset);
        
        if (offset === 0) return "text-white text-[25vw] z-10 opacity-100 scale-110 font-extrabold";
        if (absOffset === 1) return "text-gray-500 text-[15vw] z-0 opacity-90 font-extrabold";
        if (absOffset === 2) return "text-gray-600 text-[14vw] z-0 opacity-80 font-extrabold";
        if (absOffset === 3) return "text-gray-700 text-[13vw] z-0 opacity-70 font-extrabold";
        if (absOffset === 4) return "text-gray-700 text-[12vw] z-0 opacity-60 font-extrabold";
        return "text-gray-900 text-[4vw] z-0 opacity-50";
    }

    function checkDigit(digit: number) {
        if (characterString[currentCharacterIndex] == digit.toString()) {
            currentCharacterIndex++;
        }
    }
</script>

<button onclick={() => {challangemode = !challangemode}}>👁</button>

<div id="Characters" class="flex items-center justify-center w-full h-64 bg-gray-950 overflow-hidden">
  {#each visibleIndices as index (index)}
    {@const offset = index - currentCharacterIndex}
    {@const char = (challangemode && offset >= 0) ? '?' : characterString[index]}

    <div 
        animate:flip={{duration: 250, easing: cubicOut}} 
        transition:fade={{duration: 200}}
        class="w-[15vw] flex justify-center items-center"
    >
        {#if char !== undefined}
            <h1 class="{getStyles(offset)} transition-all duration-250 ease-out">
                {char}
            </h1>
        {/if}
    </div>
  {/each}
</div>

<!-- <div class="flex justify-center mt-4">
    <button onclick={() => currentCharacterIndex++} class="px-6 py-3 bg-sky-600 hover:bg-sky-500 text-white font-medium rounded-full transition-all">
        Next: {currentCharacterIndex + 1}
    </button>
</div> -->

<div class="grid grid-cols-3 gap-4 mb-8">
    {#each [9,8,7,6,5,4,3,2,1,0] as num}
        <button
            onclick={() => checkDigit(num)}
            class="
                w-16 h-16 sm:w-20 sm:h-20
                text-3xl sm:text-4xl
                border-2 border-neutral-400
                flex items-center justify-center
                hover:bg-white/10 active:bg-white/20 hover:border-white
                transition-all duration-200
                {num === 0 ? 'col-start-2' : ''}
            ">
            {num}
        </button>
    {/each}
</div>