<script lang="ts">
  import type { PageData } from "./$types";

  interface Props {
    data: PageData;
  }

  let { data }: Props = $props();
  let candies = $state(data.candies); // grab the initial value from the server
  let pending = $state(0); // number of pending requests

  async function addCandy() {
    // Update count locally.
    candies++;

    // Update count on the server.
    pending++;
    await fetch("/api/candies", {
      method: "POST",
      body: JSON.stringify({ candies: 1 }),
    });
    pending--;
  }
</script>

<svelte:head>
  <title>Home</title>
</svelte:head>

<h1>Where In The World</h1>
<div class="input-row">
<label for="userInput"> Where do you want to go?</label>
<input type= "text" id="userInput" name="userInput" placeholder="Type a city..."/>
<button class="find"> Find!</button>
</div>
<hr />

<footer class="centered">
  <p> By: Hannah Park, Lana Vu, Timothy Ou </p>
</footer>

<style lang="scss">
  .candies div {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    gap: var(--pico-spacing);

    @media (max-width: 400px) {
      flex-direction: column;
      align-items: stretch;
    }

    p {
      margin: 0;
    }
  }

  h1 {
    text-align: center;
    margin-bottom: 100px;
  }

  label {
    text-align: left;
    font-size:1.2rem;
  }

  .input-row {
    display: flex;
    align-items: center;
    gap: 20px;
    justify-content: center;
  }

  #userInput {
    width: 500px;
  }

  .find {
    margin-top: -20px;
  }
</style>
