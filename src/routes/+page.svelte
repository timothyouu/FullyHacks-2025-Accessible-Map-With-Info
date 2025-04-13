<script>
  let city = "";
  let weather = null;
  let timeInfo = null;
  let timeDifference = null;
  let places = [];
  let error = "";

  const WEATHER_API_KEY = "65a2c45c1ef7bdfc733d2ed2058ce1a0";
  const TIMEZONE_API_KEY = "0TDL2BXGDR6F";
  const GEOAPIFY_KEY = "7b589f5086bf4635b6193fa4d54ede4f";

  async function fetchAllCityInfo() {
    error = "";
    weather = null;
    timeInfo = null;
    timeDifference = null;
    places = [];

    if (!city) return;

    try {
      // 1. Get weather
      const weatherRes = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${WEATHER_API_KEY}&units=metric`);
      const weatherData = await weatherRes.json();
      if (weatherData.cod !== 200) throw new Error("Weather not found");
      weather = weatherData;

      // 2. Get lat/lon from city
      const geoRes = await fetch(`https://api.openweathermap.org/geo/1.0/direct?q=${city}&limit=1&appid=${WEATHER_API_KEY}`);
      const geoData = await geoRes.json();
      if (!geoData.length) throw new Error("City not found");

      const { lat, lon } = geoData[0];

      // 3. Get timezone info
      const timeRes = await fetch(`https://api.timezonedb.com/v2.1/get-time-zone?key=${TIMEZONE_API_KEY}&format=json&by=position&lat=${lat}&lng=${lon}`);
      const timeData = await timeRes.json();
      if (timeData.status === "FAILED") throw new Error(timeData.message);
      timeInfo = timeData;

      const userOffsetHours = -new Date().getTimezoneOffset() / 60;
      const targetOffsetHours = timeInfo.gmtOffset / 3600;
      timeDifference = targetOffsetHours - userOffsetHours;

      // 4. Get tourist places
      const placeRes = await fetch(`https://api.geoapify.com/v2/places?categories=tourism.sights,entertainment&filter=circle:${lon},${lat},5000&limit=5&apiKey=${GEOAPIFY_KEY}`);
      const placeData = await placeRes.json();

      if (!placeData.features.length) {
        places = [];
      } else {
        places = placeData.features.map(p => ({
          name: p.properties.name,
          address: p.properties.address_line1
        }));
      }
    } catch (e) {
      error = e.message || "Failed to load data.";
    }
  }
</script>

<input
  bind:value={city}
  placeholder="Enter city name (e.g., Seoul, Paris, Tokyo)"
  on:keydown={(e) => e.key === 'Enter' && fetchAllCityInfo()}
/>
<button on:click={fetchAllCityInfo}>Search</button>

{#if weather}
  <p><strong>Weather in {weather.name}:</strong> {weather.main.temp}°C</p>
{/if}

{#if timeInfo}
  <p><strong>Time zone:</strong> {timeInfo.zoneName} (UTC {timeInfo.gmtOffset / 3600 >= 0 ? "+" : ""}{timeInfo.gmtOffset / 3600})</p>
  <p><strong>Local time:</strong> {timeInfo.formatted}</p>
  <p><strong>Time difference from your location:</strong>
    {timeDifference > 0 ? `${timeDifference} hour(s) ahead` :
    timeDifference < 0 ? `${-timeDifference} hour(s) behind` :
    `Same as your time`}
  </p>
{/if}

{#if places.length}
  <h3>Top Places in {city}:</h3>
  <ul>
    {#each places as place}
      <li>
        <strong>{place.name || "Unnamed Place"}</strong><br />
        <small>{place.address}</small>
      </li>
    {/each}
  </ul>
{/if}

{#if error}
  <p style="color: red;">⚠️ {error}</p>
{/if}

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



