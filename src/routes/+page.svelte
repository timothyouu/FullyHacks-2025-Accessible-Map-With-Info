<script>
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

<img class="bgimage" src="/images/world.png" alt="world"/>
<div class="darkenimage"></div>

<h1>Where In The World</h1>
<label for="userInput"> Where do you want to go?</label>
<div class="input-row">
<input type= "text" id="userInput" name="userInput" placeholder="Type a city..."/>
<button class="find"> Find!</button>
</div>
<hr />

<footer class="centered">
  <p> By: Hannah Park, Lana Vu, Timothy Ou </p>
</footer>

<style lang="scss">
  .bgimage {
    width: 100%;
    height: 100%;
    z-index: -10;
    position: fixed;
    top: 0;
    left: 0;
  }
  .darkenimage {
    width: 100vw;
    height: 100vh;
    z-index: -5;
    position: fixed;
    top: 0;
    left: 0;
    background-color: color-mix(in srgb, black 35%, transparent 65%);
    backdrop-filter: blur(1.5px);
  }
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
    margin-top: -50px;
  }

  label {
    font-size: 1.2rem;
    display: flex;
    align-items: center;
    gap: 20px;
    justify-content: center;
  }

  .input-row {
    display: flex;
    align-items: center;
    gap: 20px;
    justify-content: center;
    margin-top: -50px;
  }

  #userInput {
    width: 500px;
    height: 50px;
    margin-top: 50px;
  }

  .find {
    margin-top: 30px;
  }
</style>
