<script>
  import CitiesFinder from '$lib/cities';

  function toTitleCase(str) {
  return str.replace(
    /\w\S*/g,
    text => text.charAt(0).toUpperCase() + text.substring(1).toLowerCase()
  );
}

  let city = $state("");
  let weather = $state(null);
  let timeInfo = $state(null);
  let timeDifference = $state(null);
  let places = $state([]);
  let error = $state("");

  const WEATHER_API_KEY = "65a2c45c1ef7bdfc733d2ed2058ce1a0";
  const TIMEZONE_API_KEY = "0TDL2BXGDR6F";
  const GEOAPIFY_KEY = "7b589f5086bf4635b6193fa4d54ede4f";

  function reset() {
  city = "";
  weather = null;
  timeInfo = null;
  timeDifference = null;
  places = [];
  error = "";
  }
  let temperature = ("");
  let isCelsius = (true);
 

 function toggleUnit() {
   isCelsius = !isCelsius;
   if (isCelsius) {
     temperature = weather.main.temp + "°C";
     return temperature;
   } else {
     temperature = ((weather.main.temp*9/5)+32).toFixed(2)  + "°F";
     return temperature;
   }
 }
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
      toggleUnit();

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
          address: p.properties.address_line2 || "Unknown Address"
        }));
      }
    } catch (e) {
      error = e.message || "Failed to load data.";
    }
  }
// const suggestions = ["Los Angeles", "Whittier"];
let filtered = $state([]);
$effect(() => {
  filtered = city ? CitiesFinder.find(city.toLowerCase()).map((c) => toTitleCase(c)) || []
                  : [];
});
</script>

{#if timeInfo || error}
<div class="message-block">
{#if weather}
  <p><strong>Temperature in {weather.name}:</strong> {temperature}</p>
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
  <p style="color: red;">{error}</p>
{/if}
<button class="resetButton hoverGrow" onclick={reset}>←</button>
</div>
<nav>
  <ul class="right">
      <li>
        <button class="temp hoverGrow" onclick={toggleUnit}>
          Switch to {isCelsius ? "°F" : "°C"}
        </button>
      </li>
  </ul>
</nav>
{/if}


<svelte:head>
  <title>Home</title>
</svelte:head>

<img class="bgimage" src="/images/world.png" alt="world"/>
<div class="darkenimage"></div>

<a class= "hoverGrow" href="/mars">
  <img src="/images/mars.png" alt="Mars" />
</a>

<h1>Where In The World</h1>
<label for="userInput"> Where do you want to go?</label>
<div class="input-row">
  <div class="suggestionsandtext">
    <input type= "text" id="userInput" name="userInput" placeholder="Type a city..." bind:value={city}/>
    {#if filtered.length > 0}
      <ul class="suggestions">
        {#each filtered as c}
          <li onclick={() => city = c}>{c}</li>
        {/each}
      </ul>
    {/if}
  </div>
  <button class="find hoverGrow" onclick={fetchAllCityInfo} > Find!</button>
</div>



<footer class="centered footer-bot">
  <hr />
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

  .footer-bot{
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translate(-50%,0px);
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
    flex-direction: row;
    align-items: start;
    gap: 20px;
    justify-content: center;
    margin-top: -50px;
  }

  #userInput {
    width: 500px;
    height: 50px;
    margin-top: 70px;
  }

  .find {
    margin-top: 62.5px;
  }

  .message-block {
  margin-top: 20px;
  height: 75vh;
  width: 80vw;
  top: 12.5vh;
  left: 10vw;
  overflow-y: auto;
  background-color: color-mix(in srgb, black 95%, transparent 5%);
  position: fixed;
  z-index: 10;
  border-radius: 1rem;
  border: white 1px solid;
  padding: 1rem;
  }
  .resetButton {
    background-color: #9f1b06;
    color: black;
    border: 1px;
    padding: 10px 20px;
    font-size: 30px;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  .temp {
    margin-left: 70vw;
    height: 45px;
    background-color: rgb(79, 79, 225);
    margin-top: 80px;
  }
  .hoverGrow {
    border-radius: 6px;
    cursor: pointer;
    transition: transform 0.3s ease;
  }
  .hoverGrow:hover{
  transform: scale(1.1);
}
  .suggestionsandtext {
    display: flex;
    align-items: start;
    justify-content: start;
    flex-direction: column;
  }
  .suggestions {
    width: 23.75rem;
    max-height: 20rem;
    overflow-y: auto;
    border: 0.5px solid gray;
  }
  .suggestions > li {
    cursor: grab;
    list-style-type: none;
    transition: transform 0.3s ease;
  }
  .suggestions > li:hover {
    font-weight: bold;
  }

  a img {
    width: 200px;
    height: 200px;
    position: absolute;
    margin-top: 70px;
  }
</style>



