<script>
  import PhotoGuesser from "./lib/PhotoGuesser.svelte";

  async function loadPhotosList() {
		const res = await fetch('https://s3.us-east-2.wasabisys.com/lmec-public-files/temp/bpl-photos-by-date.tsv');
		const text = await res.text();

		if (res.ok) {
			return text;
		} else {
			throw new Error(text);
		}
	}
	
	let photosList = loadPhotosList();


</script>

<main>
  <div>
    {#await photosList}
	<p>Loading photos data ...</p>
{:then loadedList }
	<div>
    <PhotoGuesser photosList={loadedList} />
  </div>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}


  </div>

  <div class="text-sm text-gray-800 font-light italic mt-4">Built by Garrett Dash Nelson of the <a href="https://leventhalmap.org">Leventhal Map & Education Center</a> using data from Digital Commonwealth</div>

</main>

<style>

</style>
