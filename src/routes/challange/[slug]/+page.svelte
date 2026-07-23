<script lang="js">
    import { onMount } from 'svelte';
    import { flip } from 'svelte/animate';
    import { fade } from 'svelte/transition';
    import { cubicOut } from 'svelte/easing';

    let peer;
    let conn = $state(null);
    let myPeerId = $state('');
    let targetPeerId = $state('');

    // Game state
    let currentTurn = $state('host'); // 'host' or 'guest'
    let role = $state('host');        // 'host' or 'guest'
    let won = $state(false);
    let ended = $state(false);


    onMount(async () => {
        // Dynamic import to avoid SSR issues if using SvelteKit
        const { Peer } = await import('peerjs');
        
        // Connects to PeerJS free public signaling network
        peer = new Peer();

        peer.on('open', (id) => {
        myPeerId = id;
        });

        // Listen for incoming connection (If you are the Host)
        peer.on('connection', (incomingConn) => {
        conn = incomingConn;
        role = 'host';
        setupConnectionListeners();
        });
    });

    function joinRoom() {
        if (!targetPeerId) return;
        role = 'guest';
        // Connect to Host (If you are the Guest)
        conn = peer.connect(targetPeerId);
        setupConnectionListeners();
    }

    function setupConnectionListeners() {
        conn.on('data', (data) => {
            if (!data.correct) {
                won = true;
                ended = true;
            }

            currentTurn = data.nextTurn;
            currentCharacterIndex += 1;
        });
    }

    let currentCharacterIndex = $state(2);

    let { data } = $props();

    const characterString = data.characterString;

    // Static offsets for calculation logic
    const offsets = [-4, -3, -2, -1, 0, 1, 2, 3, 4];

    // Derived state: The actual indices of the characters we want to show.
    // We map these so we can key the block by the absolute index.
    let visibleIndices = $derived(offsets.map(o => currentCharacterIndex + o));

    function getStyles(offset) {
        const absOffset = Math.abs(offset);
        
        if (offset === 0) return "text-white text-[8rem] z-10 opacity-100 scale-110 font-extrabold";
        if (absOffset === 1) return "text-gray-500 text-[6rem] z-0 opacity-90 font-extrabold";
        if (absOffset === 2) return "text-gray-600 text-[5rem] z-0 opacity-80 font-extrabold";
        if (absOffset === 3) return "text-gray-700 text-[4rem] z-0 opacity-70 font-extrabold";
        if (absOffset === 4) return "text-gray-700 text-[3rem] z-0 opacity-60 font-extrabold";
        return "text-gray-900 text-[3rem] z-0 opacity-50";
    }

    function checkDigit(digit) {
        if (currentTurn !== role) return;

        const nextTurn = role === 'host' ? 'guest' : 'host';
        currentTurn = nextTurn;
        
        if (characterString[currentCharacterIndex] == digit.toString()) {
            currentCharacterIndex++;
            
            if (conn) {
                conn.send({
                    correct: true,
                    nextTurn: nextTurn
                });
            }
        } else {
            ended = true;
            if (conn) {
                conn.send({
                    correct: false,
                    nextTurn: nextTurn
                });
            }
        }
    }

    function reset() {
        currentCharacterIndex = 2;
        won = false;
        ended = false;
    }

</script>

<style>
  .card { max-width: 400px; margin: 2rem auto; padding: 1.5rem; border: 1px solid #ccc; borderRadius: 8px; }
  .setup { display: flex; flex-direction: column; gap: 0.5rem; }
  .turn-btn { padding: 1rem 2rem; font-size: 1.1rem; cursor: pointer; width: 100%; margin-top: 1rem; }
  .turn-btn:disabled { opacity: 0.6; cursor: not-allowed; }
</style>

{#if !conn}
<div class="card">
  <h2>Doe ff je ID delen met elkaar</h2>
    <div class="setup">
      <p><strong>Jouw ID:</strong> {myPeerId || 'Generating...'}</p>

      <hr />

      <div class="join-box">
        <input 
          type="text" 
          bind:value={targetPeerId} 
          placeholder="Vul Host ID in" 
        />
        <button onclick={joinRoom}>Join Game</button>
      </div>
    </div>
</div>

{:else}
<!-- GAMEPLAY AREA -->
<div class="game">
    <p>Status: <strong>Connected!</strong> (Je bent: <em>{role}</em>)</p>
</div>

{#if !ended}
<div id="Characters" class="flex items-center justify-center w-full h-32 bg-gray-950 overflow-hidden">
  {#each visibleIndices as index (index)}
    {@const offset = index - currentCharacterIndex}
    {@const char = (offset >= 0) ? '?' : characterString[index]}

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

<p class="text-3xl sm:text-2xl">Je weet er {currentCharacterIndex - 2}</p>


<div class="grid grid-cols-3 gap-4 mb-8">
    {#each [7,8,9,4,5,6,1,2,3,0] as num}
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
                {currentTurn !== role ? 'bg-gray-600 text-gray-400 border-gray-500 cursor-not-allowed' : 'hover:bg-white/10 active:bg-white/20 hover:border-white'}

            ">
            {num}
        </button>
    {/each}
</div>
{:else}

<div class="card flex content-center flex-col">
    {#if won}
        <h2 class="text-green-600">Je hebt gewonnen!</h2>
    {:else}
        <h2 class="text-red-600">Je hebt verloren</h2>
    {/if}

    <button onclick={() => reset()} 
    class="
        p-1
        mt-1
        border-2 border-neutral-400
        flex items-center justify-center
        hover:bg-white/10 active:bg-white/20 hover:border-white
        transition-all duration-200
    ">Nog een keer</button>
</div>

{/if}
{/if}