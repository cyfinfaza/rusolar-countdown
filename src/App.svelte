<script>
  import { onMount } from "svelte";
  let daysUntil = null;
  let currentTime = null;
  let wakeLock = false;

  function toggleFullScreen() {
    if (!document.fullscreenElement) {
      document.documentElement.requestFullscreen();
    } else if (document.exitFullscreen) {
      document.exitFullscreen();
    }
  }
  function recalculate() {
    let difference = Math.ceil(
      (new Date("2025-06-29T00:00:00").getTime() - new Date().getTime()) /
        (1000 * 60 * 60 * 24)
    );
    // difference /= 7;
    difference = Math.ceil(difference * 10) / 10;
    daysUntil = difference >= 0 ? difference : null;
    currentTime = new Date(Date.now()).toLocaleString().split(",").join(" • ");
  }
  function attemptWakeLock() {
    if ("wakeLock" in navigator) {
      navigator.wakeLock
        .request("screen")
        .then((wl) => {
          wakeLock = true;
          wl.addEventListener("release", () => {
            wakeLock = false;
          });
        })
        .catch((err) => {
          console.error(`${err.name}, ${err.message}`);
        });
    }
  }
  function daysToWeeksFraction(days) {
    const fractions = ["", "¹⁄₇", "²⁄₇", "³⁄₇", "⁴⁄₇", "⁵⁄₇", "⁶⁄₇"];
    let weeks = Math.floor(days / 7);
    let remainder = days % 7;
    return weeks + (remainder ? fractions[remainder] : "");
  }
  onMount(() => {
    recalculate();
    attemptWakeLock();
    let countdownInterval = setInterval(() => recalculate(), 100);
    let wakeLockInterval = setInterval(
      () => {
        if (!wakeLock) attemptWakeLock();
      },
      1000 * 60 * 9
    );
    return () => {
      clearInterval(countdownInterval);
      clearInterval(wakeLockInterval);
    };
  });
</script>

<main>
  <h1
    on:click={() => toggleFullScreen()}
    on:keydown={(event) => {
      if (event.key === "Enter" || event.key === " ") toggleFullScreen();
    }}
    tabindex="0"
  >
    {Math.floor(daysUntil / 7) ?? "--"}
  </h1>
  <p>{Math.floor(daysUntil / 7) === 1 ? "week" : "weeks"} until FSGP</p>
  <p class="currentTime">
    {currentTime ?? "--"}
  </p>
  <span
    class="wl_ind"
    on:click={() => attemptWakeLock()}
    on:keydown={(event) => {
      if (event.key === "Enter" || event.key === " ") attemptWakeLock();
    }}
    tabindex="0"
    style:color={wakeLock ? "#00ff0033" : "#ff000033"}
  >
    WL
  </span>
</main>

<style>
  h1 {
    font-size: 23vw;
    line-height: 90%;
    margin: 0;
    margin-bottom: 2vw;
    cursor: none;
  }
  p {
    font-style: italic;
    font-size: 5vw;
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

  .wl_ind {
    position: fixed;
    bottom: 2em;
    right: 2em;
    cursor: pointer;
  }
</style>
