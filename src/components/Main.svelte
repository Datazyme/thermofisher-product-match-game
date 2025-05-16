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

  function shuffle(images: string[]): Card[] {
    return [...images]
      .map((value) => ({ value, id: Math.random() }))
      .sort(() => Math.random() - 0.5);
  }

  function flipCard(index: number) {
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
  }

  function resetGame() {
    cards = shuffle(imageNames);
    flipped = [];
    matched = [];
  }

  onMount(() => resetGame());
</script>

<main class="min-h-screen min-w-screen bg-gray-900 text-white flex flex-col items-center py-10">
  <h1 class="text-3xl font-bold mb-6"> Match Game</h1>

  <div class="grid grid-cols-4 gap-2 lg:grid-cols-5 md:gap-20 sm:gap-2 mx-8 mt-10">
    {#each cards as card, i}
      <button type="button" on:click={() => flipCard(i)} class="w-20 h-20 perspective">
        <div
        class="w-full h-full transition-transform hover:scale-220 hover: z-1000 duration-500 transform-style-3d transition-transform duration-300"
        class:rotate-y-180={flipped.includes(i) || matched.includes(card.id)}
      >
        <!-- Card back -->
        <div
          class="absolute w-full h-full bg-white rounded flex items-center text-sm sm: text-xs text-rose-700 justify-center backface-hidden sm: scale-50 md:scale-150"
        >
          ThermoFisher Scientific
        </div>
          <img
            src={`/images/${card.value}`}
            alt="card"
            class="absolute w-full h-full backface-hidden rounded transform rotate-y-180 object-cover"
          />
        </div>
      </button>
    {/each}
  </div>

  <button
    on:click={resetGame}
    class="mt-6 px-4 py-2 bg-red-500 hover:bg-red-700 text-white font-bold rounded"
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
