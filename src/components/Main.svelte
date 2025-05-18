<script lang="ts">
	import { preloadCode } from "$app/navigation";
  import { onMount } from "svelte";

  type Card = {
    value: string;
    id: number;
  };

  const matchMap: Record<string, string> = {
  "DMEMText.jpg": "smoothMuscle.jpg",
  "smoothMuscle.jpg": "DMEMText.jpg",
  "neurobasalMediaText.jpg": "neuronDiff.jpg",
  "neuronDiff.jpg": "neurobasalMediaText.jpg",
  "leukemiaCells.jpg": "rpmiText.jpg",
  "rpmiText.jpg": "leukemiaCells.jpg",
  "thermalCyclerText.jpg": "PCR.jpg",
  "PCR.jpg": "thermalCyclerText.jpg",
  "iBlot.jpg": "gelTransfer.jpg",
  "gelTransfer.jpg": "iBlot.jpg",
  "dnaLadder.jpg": "gelLadder.jpg",
  "gelLadder.jpg": "dnaLadder.jpg",
  "proteinLadder.jpg": "pageRuler.jpg",
  "pageRuler.jpg": "proteinLadder.jpg",
  "platinumSuperfi2.jpg": "DNAPolymerase.jpg",
  "DNAPolymerase": "platinumSuperfi2.jpg"
};

  const imageNames: string[] = ["DMEMText.jpg", "smoothMuscle.jpg", "neurobasalMediaText.jpg", "neuronDiff.jpg", "leukemiaCells.jpg", "rpmiText.jpg", "thermalCyclerText.jpg", "PCR.jpg", "gelTransfer.jpg", "iBlot.jpg", "dnaLadder.jpg", "gelLadder.jpg", "proteinLadder.jpg", "pageRuler.jpg", "platinumSuperfi2.jpg", "DNAPolymerase.jpg"];
  let cards: Card[] = [];
  let flipped: number[] = [];
  let matched: number[] = [];
  let fullscreenIndex: number | null = null;
  let timer = 0;
  let timerInterval: number;
  let isRunning = false;

  function startTimer() {
    if (!isRunning) {
      isRunning = true;
      timerInterval = setInterval(() => {
        timer += 1;
      }, 1000);
    }
  }

  function formatTime(seconds: number): string {
    const m = Math.floor(seconds / 60).toString().padStart(2, '0');
    const s = (seconds % 60).toString().padStart(2, '0');
    return `${m}:${s}`;
  }

  function shuffle(images: string[]): Card[] {
    return [...images]
      .map((value) => ({ value, id: Math.random() }))
      .sort(() => Math.random() - 0.5);
  }

  function flipCard(index: number) {
    startTimer();
    fullscreenIndex = index;

    setTimeout(() => {
    fullscreenIndex = null;
    }, 2000); // show fullscreen for 1 second

    if (flipped.length === 2 || flipped.includes(index) || matched.includes(cards[index].id)) return;

    flipped = [...flipped, index];

    if (flipped.length === 2) {
      const [i1, i2] = flipped;
      const val1 = cards[i1].value;
      const val2 = cards[i2].value;
      const isMatch = matchMap[val1] === val2;

      setTimeout(() => {
        if (isMatch) {
          matched.push(cards[i1].id, cards[i2].id);
        }
        flipped = [];
      }, 800);
    }

    if (matched.length === cards.length) {
      clearInterval(timerInterval);
      isRunning = false;
    }

  }

  function resetGame() {
    cards = shuffle(imageNames);
    flipped = [];
    matched = [];
    clearInterval(timerInterval);
    timer = 0;
    isRunning = false;
  }

  onMount(() => resetGame());
</script>

<main class="min-h-screen min-w-screen bg-gray-900 text-white flex flex-col items-center px-4 py-6 sm:py-10">
  <h1 class="text-rose-700 text-2xl sm:text-3xl md:text-4xl font-bold mb-6">ThermoFisher Scientific</h1>
  <h1 class="text-2xl text-gray-200 sm:text-2xl md:text-3xl font-bold mb-6"> Match Game</h1>
  <h1 class="text-lg text-center">Match the products that go with each other or with their function</h1>
  {#if fullscreenIndex !== null}
    <div class="fixed inset-0 bg-black opacity-70 z-40 pointer-events-none transition-opacity duration-500"></div>
  {/if}

  <div class="text-center text-lg sm:text-xl font-semibold mb-4 mt-4">
    Time: {formatTime(timer)}
  </div>
  

  <div class="relative w-full h-full max-w-xl mx-auto lg:p-2">
    <div class="grid grid-cols-3 md:grid-cols-4 lg:grid-cols-4 gap-4">
      {#each cards as card, i}
        <button type="button" on:click={() => flipCard(i)} class={`transition-all duration-500 ease-in-out 
          ${fullscreenIndex === i 
            ? 'absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-45 h-45 md:w-55 md:h-55 lg:w-full lg:h-full z-40'
            : 'relative w-24 h-24 sm:w-28 sm:h-28 md:w-32 md:h-32'}
        `}>
          <div
          class="relative w-full h-full transition-transform duration-500 transform-style-3d"
          class:rotate-y-180={flipped.includes(i) || matched.includes(card.id)}
        >
          <!-- Card back -->
          <div
            class="absolute w-full h-full bg-white rounded flex items-center text-sm sm:text-xs text-rose-700 justify-center backface-hidden"
          >
            ThermoFisher Scientific
          </div>
            <img
              src={`/images/${card.value}`}
              alt="card"
              class="absolute w-full h-full object-cover rounded backface-hidden transform rotate-y-180"
            />
          </div>
        </button>
      {/each}
    </div>

  </div>

  <button
    on:click={resetGame}
    class="mt-10 px-4 py-2 bg-green-500 hover:bg-red-500 text-black font-bold rounded"
  >
    Reset
  </button>
</main>

<style>
  .perspective {
    perspective: 1000px;
  }


  .transform-style-3d {
    transform-style: preserve-3d;
  }

  .backface-hidden {
    backface-visibility: hidden;
  }

  .rotate-y-180 {
    transform: rotateY(180deg);
  }

  .rotate-y-180 img {
    transform: rotateY(180deg);
  }
</style>
