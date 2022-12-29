<script>
  import {onMount} from "svelte";
  let lyrics=[];
  let time = 0;
	let duration;
	let paused = true;
  onMount(async ()=>{
    const response = await fetch("lyrics/kryptonite.txt");
    const data = await response.text();
    const splitted = data.split("\n")
    const x = splitted.map(line=>{
      const [time, lyric] = line.split("]")
      return {
        time:getTimeInSeconds(time),
        lyric
      }
    })
    lyrics = x;
  })
  $: timeInMS = Math.floor(time*1000);
  function getTimeInSeconds(str){
    let cleaned = str.replace("[","");
    let [minutes,rest] = cleaned.split(":")
    let [seconds, milliseconds] = rest.split(".")
    let total = Number(minutes) * 60 * 1000 + Number(seconds) * 1000 + Number(milliseconds) * 10
    return total;
  }
  //[00:20.05]
</script>

<main>
 <audio controls 
    bind:currentTime={time}
		bind:duration
		bind:paused>
  <source src="audio/kryptonite.mp3" type="audio/mpeg"></audio> 
<!-- <p>{timeInMS}</p> -->
 {#each lyrics as lyric (lyric.time)}
    <p data-time={lyric.time} class:current={timeInMS>lyric.time}>{lyric.lyric}</p>
 {/each}
</main>

<style global>
  .current {
    color:hotpink;
  }
  
  .current {
    font-weight:bold;
  }
  /* .current + .current {
    font-weight:normal;
  } */
    
  /* .current:has( + :not(.current)) {
      font-weight:bold;
    } */
  
</style>