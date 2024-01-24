<script>
  import { onMount } from "svelte";
  let daysUntil = null;
  let currentTime = null;

  function toggleFullScreen() {
    if (!document.fullscreenElement) {
      document.documentElement.requestFullscreen();
    } else if (document.exitFullscreen) {
      document.exitFullscreen();
    }
  }
  function recalculate() {
    const difference = Math.ceil(
      (new Date("2024-07-16").getTime() - new Date().getTime()) /
        (1000 * 60 * 60 * 24)
    );
    daysUntil = difference >= 0 ? difference : null;
    currentTime = new Date(Date.now()).toLocaleString().split(",").join(" • ");
  }
  onMount(() => {
    recalculate();
    let countdownInterval = setInterval(() => recalculate(), 100);
    return () => clearInterval(countdownInterval);
  });
</script>

<main>
  <h1 on:click={() => toggleFullScreen()} tabindex="0">{daysUntil ?? "--"}</h1>
  <p>days until FSGP</p>
  <p class="currentTime">{currentTime ?? "--"}</p>
</main>

<style>
  h1 {
    font-size: 20vw;
    line-height: 90%;
    margin: 0;
    margin-bottom: 2vw;
  }
  p {
    font-style: italic;
    font-size: 3vw;
    margin: 0;
  }
  .currentTime {
    position: fixed;
    bottom: 2em;
    right: 0;
    font-size: 1em;
    width: 100%;
    text-align: center;
    margin: 0;
    color: #ffffffaa;
  }
</style>
