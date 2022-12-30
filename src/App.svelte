<script>
  import { onMount } from "svelte";
  let lyrics = [];
  let time = 0;
  let track = "kryptonite";
  let player;
  onMount(getData);
  async function getData() {
    const data = await fetch(`lyrics/${track}.txt`).then((res) => res.text());
    player.src = `audio/${track}.mp3`;

    lyrics = data.split("\n").map((line) => {
      const [time, lyric] = line.split("]");
      return {
        time: getTimeInSeconds(time),
        lyric,
      };
    });
  }
  function getTimeInSeconds(str) {
    let [minutes, seconds, milliseconds] = str.replace("[", "").split(/:|\./);
    return minutes * 60 + +seconds + milliseconds / 100;
  }
</script>

<main>
  <div>
    <select bind:value={track} on:change={getData}>
      <option value="kryptonite">Kryptonite</option>
      <option value="killingit">Killing It</option>
    </select>
  </div>

  <audio controls bind:currentTime={time} preload="auto" bind:this={player} />

  {#each lyrics as lyric (lyric.time)}
    <p data-time={lyric.time} class:current={time > lyric.time}>
      {lyric.lyric}
    </p>
  {/each}
</main>

<style>
  .current {
    color: hotpink;
    font-weight: normal;
  }
  .current:not(:has(~ .current)) {
    font-weight: bold;
  }
</style>
