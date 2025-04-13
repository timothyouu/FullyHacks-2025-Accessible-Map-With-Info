<script lang="ts">
  let city = "";
  let weather = null;
  let units = "metric"; // default: Celsius
  let unitLabel = "°C"; // temperature unit symbol

  async function getWeather() {
    if (!city) return;
    const res = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=65a2c45c1ef7bdfc733d2ed2058ce1a0&units=${units}`
    );
    const data = await res.json();
    weather = data;
  }

  function handleKeyDown(event) {
    if (event.key === "Enter") {
      getWeather();
    }
  }

  function toggleUnit() {
    if (units === "metric") {
      units = "imperial";
      unitLabel = "°F";
    } else {
      units = "metric";
      unitLabel = "°C";
    }

    // Refresh weather after changing unit
    if (city) getWeather();
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
