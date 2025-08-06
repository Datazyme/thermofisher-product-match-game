<script lang="ts">
	import { preloadCode } from "$app/navigation";
  import { onMount } from "svelte";

  type Card = {
    value: string;
    id: number;
  };

  //matchMap is a TypeScript object. Record<string, string> matches two different strings.
  //This is used to match images with different values. So object for each match
  //First string is a KEY that refers to an image file and the second is the VALUE referring to match image.
  const matchMap: Record<string, string> = {
  "neurobasalMediaText.jpg": "neuronDiff.jpg",
  "neuronDiff.jpg": "neurobasalMediaText.jpg",
  "leukemiaCells.jpg": "rpmiText.jpg",
  "rpmiText.jpg": "leukemiaCells.jpg",
  "thermalCyclerText.jpg": "platinumSuperfi2.jpg",
  "platinumSuperfi2.jpg": "thermalCyclerText.jpg",
  "iBlot.jpg": "gelTransfer.jpg",
  "gelTransfer.jpg": "iBlot.jpg",
  "dnaLadder.jpg": "electrophoresisSystem.jpg",
  "electrophoresisSystem.jpg": "dnaLadder.jpg",
  "proteinLadder.jpg": "pageRuler.jpg",
  "pageRuler.jpg": "proteinLadder.jpg",
};

  const imageNames: string[] = ["neurobasalMediaText.jpg", "neuronDiff.jpg", "leukemiaCells.jpg", "rpmiText.jpg", "thermalCyclerText.jpg", "gelTransfer.jpg", "iBlot.jpg", "dnaLadder.jpg", "proteinLadder.jpg", "pageRuler.jpg", "platinumSuperfi2.jpg", "electrophoresisSystem.jpg"];
  let cards: Card[] = []; //holds card objects referencing images in imageNames
  let flipped: number[] = []; //tracks indices of flipped cards which are pushed into this array
  let matched: number[] = []; //stores indices of matched cards from flipped, are then ignored by "flipped"
  let fullscreenIndex: number | null = null; //displays flipped card zoomed in. Once timer runs out it is reset to null.
  let timer = 0; //timer for entire game in seconds, counts up every second
  let timerInterval: number; //reference to inveralID, which incremenets timer in seconds, updates timer above, can be cleared to stop timer
  let isRunning = false; //

  //function that starts timer, sets interval and updates timer
  function startTimer() {
    if (!isRunning) {
      isRunning = true;
      timerInterval = setInterval(() => {
        timer += 1;
      }, 1000);
    }
  }

  //To display elapsed time, converts seconds to minutes, converts to string to pad to two digits
  function formatTime(seconds: number): string {
    const m = Math.floor(seconds / 60).toString().padStart(2, '0');
    const s = (seconds % 60).toString().padStart(2, '0');
    return `${m}:${s}`;
  }

  //Shuffles cards (strings) from imageNames and returns the new/shuffled array Card[] of card objects. Each has random id.
  function shuffle(images: string[]): Card[] {
    return [...images] //clones images from imageNames to create new array using spread operator
      .map((value) => ({ value, id: Math.random() })) //transforms image filename into card object. use image filename (value) and generating random/unique id for each card to make object value: "", id: ""
      .sort(() => Math.random() - 0.5); //shuffles array more or less randomly.
  }

  //takes index (index specified as number data type) of clicked card in cards array to flip, calls start timer, triggers fullscreenIndex
  function flipCard(index: number) {
    startTimer();
    fullscreenIndex = index;

    //sets time fullscreenIndex is displayed, currently 2sec.
    setTimeout(() => {
    fullscreenIndex = null;
    }, 2000); // show fullscreen for 2 seconds

    //checks if flipped array is true for any of these and if so exits function (return). 
    //So if flipped array already has two cards or already contains index of flipped card or is alredy in matched array
    if (flipped.length === 2 || flipped.includes(index) || matched.includes(cards[index].id)) return;

    flipped = [...flipped, index]; //adds flipped cards index to the flipped array when above if statement is met

    //checks for match based on matchMap array of objects.
    if (flipped.length === 2) {
      const [i1, i2] = flipped;
      const val1 = cards[i1].value;
      const val2 = cards[i2].value;
      const isMatch = matchMap[val1] === val2;

      //sets time display of both cards (800s), if isMatch is true pushes cards into matched array
      setTimeout(() => {
        if (isMatch) {
          matched.push(cards[i1].id, cards[i2].id);
        }
        flipped = []; //resets flipped array
      }, 800);
    }

    //end game. checks to see if all cards are matched including the last two cards displayed (+2) which match by default. stops timer.
    if (matched.length + 2 === cards.length) {
      clearInterval(timerInterval);
      isRunning = false;
    }

  }

  //resets game, empties relevant arrays and shuffles.
  function resetGame() {
    cards = shuffle(imageNames);
    flipped = [];
    matched = [];
    clearInterval(timerInterval);
    timer = 0;
    isRunning = false;
  }

  //svelte component similar to componentDidMount() in react. sets/resets game. 
  // Makes game ready to play immediatly upon loading in browser, shuffles cards, resets timer
  onMount(() => resetGame());
</script>

<main class="min-h-screen min-w-screen bg-gray-900 text-white flex flex-col items-center px-4 py-6 sm:py-10">
  <h1 class="text-rose-700 text-2xl sm:text-3xl md:text-4xl font-bold mb-2">ThermoFisher Scientific</h1>
  <h1 class="text-2xl text-gray-200 sm:text-2xl md:text-3xl font-bold mb-2"> Match Game</h1>
  <h1 class="text-lg text-center">Match the products that go with each other or with their function</h1>
  {#if fullscreenIndex !== null}
    <div class="fixed inset-0 bg-black opacity-70 z-40 pointer-events-none transition-opacity duration-500"></div>
  {/if}

  <div class="text-center text-lg sm:text-xl font-semibold mb-2 mt-2">
    Time: {formatTime(timer)}
  </div>
  

  <div class="relative w-full h-full max-w-xl mx-auto lg:p-2">
    <div class="grid grid-cols-3 md:grid-cols-4 lg:grid-cols-4 gap-4">
      {#each cards as card, i}
        <button type="button" on:click={() => flipCard(i)} class={`transition-all duration-500 ease-in-out 
          ${fullscreenIndex === i 
            ? 'absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-45 h-45 md:w-55 md:h-55 lg:w-45 lg:h-45 z-40'
            : 'relative w-24 h-24 sm:w-28 sm:h-28 md:w-32 md:h-32 lg:h-24 lg:w-24'}
        `}>
          <div
          class="relative w-full h-full transition-transform duration-500 transform-style-3d"
          class:rotate-y-180={flipped.includes(i) || matched.includes(card.id)}
        >
          <!-- Card back -->
          <div
            class="absolute w-25 h-25 bg-white rounded flex items-center text-sm sm:text-xs text-rose-700 justify-center backface-hidden"
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
  <h5 class="mt-10 text-sm">Created by Anastasia Kuzmin <span class="text-violet-400">(anastasia.kuzmin@thermofisher.com)</span></h5>
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
