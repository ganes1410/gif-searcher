<script>
  import { fade } from "svelte/transition";

  let searchText = "";
  let loading = false;
  let error = null;
  let resultGifs = [];
  let searchType = "gifs";

  // console.log(API_KEY);

  async function handleSubmit() {
    let API_URL = `https://api.giphy.com/v1/${searchType}/search?api_key=z7CWngCHf50zCsPWC0qMHRZ42qriBPWy&q=${searchText}`;
    if (searchText.length >= 3) {
      try {
        loading = true;
        resultGifs = [];
        const gifResult = await fetch(API_URL);
        const json = await gifResult.json();
        resultGifs = json.data.map(data => data.images.fixed_height.url);
        loading = false;
        console.log(resultGifs);
      } catch (error) {
        error = error;
        loading = false;
      }
    }
  }

  // Copy to clipboard
  function copyToClipboard(url) {
    navigator.clipboard.writeText(url).then(
      function() {
        alert("copied successfully");
        /* clipboard successfully set */
      },
      function() {
        /* clipboard write failed */
        alert("copying failed");
      }
    );
  }
</script>

<style>
  button.submit-btn {
    margin-right: 20px;
  }
  button:disabled {
    opacity: 0.4;
  }
  .results {
    display: flex;
    flex-wrap: wrap;
    width: 100vw;
    justify-content: space-around;
  }
  img {
    width: 100%;
    height: 100%;
  }
  button.copy {
    position: absolute;
    top: 8px;
    right: 8px;
    cursor: pointer;
  }

  .gif-container {
    position: relative;
    width: 400px;
    height: 300px;
    background: grey;
    margin: 8px 0;
  }
</style>

<svelte:head>
  <title>Gif searcher</title>
</svelte:head>

<form on:submit|preventDefault={handleSubmit}>
  <input
    bind:value={searchText}
    type="text"
    placeholder={`search for  ${searchType}`} />
  <button
    type="submit"
    class="submit-btn"
    disabled={searchText.trim().length < 3}>
    Submit
  </button>
  Search By:
  <select name="searchby" id="searchTypeSelect" bind:value={searchType}>
    <option value={'gifs'}>gifs</option>
    <option value={'stickers'}>stickers</option>
  </select>
</form>

<!-- Loading Case -->
{#if loading}
  <h2>Loading</h2>
{/if}

<!-- Error Case -->
{#if error}
  <h2>Some Error Occured. Please Try Again</h2>
{/if}

<!-- Results -->
{#if resultGifs.length > 0 && !error}
  <div class="results">
    {#each resultGifs as result}
      <div class="gif-container" transition:fade>

        <button class="copy" on:click={() => copyToClipboard(result)}>
          Copy Url
        </button>

        <img alt="gif" src={result} />
      </div>
    {/each}
  </div>
{/if}
