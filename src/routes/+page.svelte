<script lang="ts">
  import Hls from 'hls.js';

  let playing = $state(false);
  let progress = $state(0);
  let audio: HTMLAudioElement;

  function start() {
    playing = true;

    if (Hls.isSupported()) {
      const hls = new Hls({
        maxBufferLength: 30,
        maxMaxBufferLength: 60,
      });
      hls.loadSource('/audio/playlist.m3u8');
      hls.attachMedia(audio);
      hls.on(Hls.Events.MANIFEST_PARSED, () => audio.play());
    } else if (audio.canPlayType('application/vnd.apple.mpegurl')) {
      audio.src = '/audio/playlist.m3u8';
      audio.addEventListener('loadedmetadata', () => audio.play());
    }

    audio.addEventListener('timeupdate', () => {
      if (audio.duration) {
        progress = (audio.currentTime / audio.duration) * 100;
      }
    });
  }

  function seek(e: MouseEvent) {
    if (audio?.duration) {
      audio.currentTime = (e.clientX / window.innerWidth) * audio.duration;
    }
  }
</script>

<svelte:head>
  <title>FREE STRESS</title>
</svelte:head>

<div class="screen">
  <div class="dvd">FREE STRESS</div>

  {#if !playing}
    <button class="overlay" onclick={start}>
      <div class="play-btn"></div>
    </button>
  {/if}

  <audio bind:this={audio}></audio>

  <!-- svelte-ignore a11y_no_static_element_interactions -->
  <div class="progress" onclick={seek} onkeydown={() => {}}>
    <div class="progress-fill" style="width: {progress}%"></div>
  </div>
</div>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    background: #000;
    overflow: hidden;
    cursor: none;
  }

  .screen {
    width: 100vw;
    height: 100vh;
    position: relative;
  }

  .dvd {
    --size: 200px;
    width: var(--size);
    height: var(--size);
    position: absolute;
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: 'Courier New', monospace;
    font-size: 1.4rem;
    font-weight: bold;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    color: #fff;
    border: 2px solid #fff;
    animation:
      dvd-x 7.3s linear infinite alternate,
      dvd-y 11.7s linear infinite alternate,
      dvd-hue 23s linear infinite;
  }

  @keyframes dvd-x {
    0%   { left: 0; }
    100% { left: calc(100vw - var(--size)); }
  }

  @keyframes dvd-y {
    0%   { top: 0; }
    100% { top: calc(100vh - var(--size)); }
  }

  @keyframes dvd-hue {
    0%   { color: #ff0040; border-color: #ff0040; }
    25%  { color: #00ff88; border-color: #00ff88; }
    50%  { color: #4080ff; border-color: #4080ff; }
    75%  { color: #ff00ff; border-color: #ff00ff; }
    100% { color: #ff0040; border-color: #ff0040; }
  }

  .overlay {
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.8);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 100;
    cursor: pointer;
    border: none;
    transition: opacity 0.5s;
  }

  .play-btn {
    width: 80px;
    height: 80px;
    border: 2px solid #fff;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: transform 0.2s;
  }

  .play-btn:hover {
    transform: scale(1.1);
  }

  .play-btn::after {
    content: '';
    display: block;
    width: 0;
    height: 0;
    border-style: solid;
    border-width: 15px 0 15px 28px;
    border-color: transparent transparent transparent #fff;
    margin-left: 5px;
  }

  .progress {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 3px;
    background: rgba(255, 255, 255, 0.1);
    z-index: 50;
    cursor: pointer;
  }

  .progress-fill {
    height: 100%;
    background: #fff;
    transition: width 0.3s linear;
  }
</style>
