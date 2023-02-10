<script>
  export let photosList;

  const parsedList = photosList.split("\n");

  function chooseRandom() {
    return parsedList[Math.floor(Math.random() * parsedList.length)].split(
      "\t"
    );
  }

  let selectedChoice = chooseRandom();

  async function loadManifest() {
    const res = await fetch(
      `https://www.digitalcommonwealth.org/search/${selectedChoice[0]}/manifest`
    );
    const mf = await res.json();

    if (res.ok) {
      return mf.sequences[0].canvases[0].images[0].resource["@id"];
    } else {
      throw new Error(mf);
    }
  }

  let mf = loadManifest();

  function refresh() {
    chosenNumber = "";
    revealAnswer = false;
    selectedChoice = chooseRandom();
    mf = loadManifest();
  }

  let chosenNumber = ""
  $: chosenNumber = chosenNumber.replace(/[^0-9]/g, '');

  let revealAnswer = false
  function makeGuess() {
    if(+chosenNumber < 1850 || +chosenNumber > 2023) {
      window.alert("Enter a year between 1850 and 2023")
    } else {
      revealAnswer = true;
    }

  }

</script>

<div>
  <h1 class="text-3xl mb-4">When was this photo taken?</h1>

  {#await mf}
    <p>Loading photo ...</p>
  {:then mfImage}
    <div class="text-center">
      <img id="main-image" src={mfImage} alt="From Digital Commonwealth" />
    </div>
  {:catch error}
    <p style="color: red">{error.message}</p>
  {/await}

  <div>
    <span>Guess a year:</span> <input type="text" bind:value={chosenNumber} maxlength="4" size="4" class="m-4 bg-gray-50 border rounded border-gray-600 p-2 text-lg font-bold" placeholder="yyyy"><button class="border rounded border-blue-800 p-2 text-lg bg-blue-100 hover:bg-blue-200" on:click={makeGuess}>🧐 Guess!</button>
  </div>
  {#if revealAnswer}
  <div class="bg-gray-50 my-2 p-3">
    This photo is from {selectedChoice[1]}. You were {Math.abs(+chosenNumber-selectedChoice[1])} years off. <a class="text-orange-800 text-semibold" href="https://www.digitalcommonwealth.org/search/{selectedChoice[0]}" target="_blank">See this photo in Digital Commonwealth ➡️</a>
  </div>
  {/if}
  <button on:click={refresh} class="border border-2 rounded border-green-600 p-2 bg-green-50 hover:bg-green-100">🎲 Pick a new random photo</button>
</div>

<style>
  #main-image {
    max-height: 60vh;
  }
</style>
