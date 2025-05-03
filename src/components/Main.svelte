<script lang="ts">
  import { onMount } from "svelte";

  type Card = {
    value: string;
    id: number;
  };

  const matchMap: Record<string, string> = {
  "media1.jpeg": "media2.jpeg",
  "media2.jpeg": "media1.jpeg",
  "media3.jpeg": "media4.jpeg",
  "media5.jpeg": "media6.jpeg",
};

  const imageNames: string[] = ["media1.jpeg", "media2.jpeg", "media3.jpeg", "media4.jpeg", "media5.jpeg", "media6.jpeg"];
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

<main class="min-h-screen bg-gray-900 text-white flex flex-col items-center py-10">
  <h1 class="text-3xl font-bold mb-6">🃏 Match Game</h1>

  <div class="grid grid-cols-4 gap-4">
    {#each cards as card, i}
      <button type="button" on:click={() => flipCard(i)} class="w-24 h-24 perspective">
        <div
        class="relative w-full h-full transition-transform duration-500 transform-style-3d"
        class:rotate-y-180={flipped.includes(i) || matched.includes(card.id)}
      >
        <!-- Card back -->
        <div
          class="absolute w-full h-full bg-blue-700 rounded flex items-center justify-center backface-hidden"
        >
          ❓
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
