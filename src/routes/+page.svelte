<script lang="ts">
  import Hls from 'hls.js';

  let started = $state(false);
  let audio: HTMLAudioElement;

  // One gesture to begin — browsers block audio autoplay, and the concept
  // is that you then listen to the whole track with no controls.
  function start() {
    if (started) return;
    started = true;

    if (Hls.isSupported()) {
      const hls = new Hls({ maxBufferLength: 30, maxMaxBufferLength: 60 });
      hls.loadSource('/audio-v2/playlist.m3u8');
      hls.attachMedia(audio);
      hls.on(Hls.Events.MANIFEST_PARSED, () => audio.play());
    } else if (audio.canPlayType('application/vnd.apple.mpegurl')) {
      audio.src = '/audio-v2/playlist.m3u8';
      audio.addEventListener('loadedmetadata', () => audio.play());
    }
  }
</script>

<svelte:head>
  <title>FREE STRESS</title>
</svelte:head>

<div class="screen">
  <!-- Dimi's graphic, swap static/free-stress.svg to change it.
       The SVG is used as a mask so we can colour-cycle the shape itself. -->
  <div class="dvd"></div>

  <audio bind:this={audio}></audio>

  {#if !started}
    <button class="start" onclick={start} aria-label="Play">
      <span class="play-icon"></span>
      <span class="play-word">play</span>
    </button>
  {/if}
</div>

<style>
  @font-face {
    font-family: 'OCR-X';
    src: url('/fonts/OCR-X-Light.otf') format('opentype');
    font-weight: 300;
    font-display: swap;
  }

  :global(body) {
    margin: 0;
    padding: 0;
    background: #000;
    overflow: hidden;
  }

  .screen {
    width: 100vw;
    height: 100vh;
    position: relative;
    cursor: none;
  }

  .dvd {
    /* Box is sized to the artwork's exact aspect ratio (2.388:1) so its
       edges are the logo's edges — that's what makes the corner-hits land. */
    --w: min(420px, 70vw);
    --h: calc(var(--w) / 2.3881);
    width: var(--w);
    height: var(--h);
    position: absolute;
    /* The graphic shape, cut out of a colour-cycling fill. */
    -webkit-mask: url(/free-stress.svg) center / contain no-repeat;
    mask: url(/free-stress.svg) center / contain no-repeat;
    animation:
      dvd-x 7.3s linear infinite alternate,
      dvd-y 11.7s linear infinite alternate,
      dvd-hue 5s linear infinite;
  }

  @keyframes dvd-x {
    0%   { left: 0; }
    100% { left: calc(100vw - var(--w)); }
  }

  @keyframes dvd-y {
    0%   { top: 0; }
    100% { top: calc(100vh - var(--h)); }
  }

  @keyframes dvd-hue {
    0%   { background-color: #ff0040; color: #ff0040; }
    25%  { background-color: #00ff88; color: #00ff88; }
    50%  { background-color: #4080ff; color: #4080ff; }
    75%  { background-color: #ff00ff; color: #ff00ff; }
    100% { background-color: #ff0040; color: #ff0040; }
  }

  /* Single start gesture — full-screen, fades itself out on click. */
  .start {
    position: fixed;
    inset: 0;
    z-index: 100;
    display: flex;
    align-items: center;
    justify-content: center;
    border: none;
    background: rgba(0, 0, 0, 0.85);
    cursor: pointer;
    gap: 16px;
    color: #fff;
    transition: transform 0.2s;
  }

  .start:hover {
    transform: scale(1.08);
  }

  /* White play triangle before the word. */
  .play-icon {
    width: 0;
    height: 0;
    border-style: solid;
    border-width: 12px 0 12px 20px;
    border-color: transparent transparent transparent #fff;
  }

  /* "play" in Dimi's font, plain white. */
  .play-word {
    font-family: 'OCR-X', 'Courier New', monospace;
    font-size: 2.4rem;
    letter-spacing: 0.25em;
    text-transform: lowercase;
    color: #fff;
  }
</style>
