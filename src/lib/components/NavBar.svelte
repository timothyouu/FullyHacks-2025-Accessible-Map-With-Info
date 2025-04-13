<script>
let isCelsius = $state(true);
let temperature = $state("");

  function toggleUnit() {
    isCelsius = !isCelsius;
    if (isCelsius) {
      temperature = "25°C";
    } else {
      temperature = "77°F";
    }
  }

</script>

<nav>
  <ul class="right">
      <li>
        <button onclick={toggleUnit}>
          Switch to {isCelsius ? "°F" : "°C"}
          <p>Temperature: {temperature}</p>
        </button>
      </li>
  </ul>
</nav>

<style>
  a[href] {
    color: var(--pico-secondary);
  }

  a[href].active {
    color: var(--pico-primary);
    font-weight: bold;
  }
  button {
    margin-left:70vw;
    height: 80px;
  }
</style>
