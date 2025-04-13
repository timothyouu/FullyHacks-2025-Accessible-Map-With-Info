<script>

</script>
<img class="bgimage" src="/images/Fullyhackz.png" alt="world"/>
<div class="darkenimage"></div>

<a class= "hoverGrow" href="/">
  <img src="/images/earth.png" alt="Earth" />
</a>


<label for="userInput"> You are stuck here now ☺</label>
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
    margin-top: 75px;
  }
  .hoverGrow {
    border-radius: 6px;
    cursor: pointer;
    transition: transform 0.3s ease;
  }
  .hoverGrow:hover{
  transform: scale(1.1);
}
  a img {
    width: 200px;
    height: 200px;
    position: absolute;
    margin-top: 70px;
    margin-left: 65%;
  }
</style>