<script lang="ts">
  import Reel from './Reel.svelte';
  import Options, { getDefaultOptions } from './Options.svelte';
  import mamano from './assets/mamano.json';
  import { onMount } from 'svelte';

  let reels: Reel[] = [];
  let reelCount = 3;
  let options = getDefaultOptions();
  let optOpen = false;
  let spinning = false;
  let images = mamano.map(m =>({ src: m.img, name: m.name }));

  function getQualifyingMamano(type: string[], franchise: string[]) {
    const result = mamano.map((m, i) => type.some(t => m.opts.type.includes(t)) && franchise.includes(m.opts.franchise) ? i : -1).filter(i => i !== -1);
    return result.length ? result : [-1];
  }

  async function rollReels() {
    if (spinning) return;
    spinning = true;

    const typeOptions = Object.keys(options.type).filter(k => options.type[k]);
    const franchiseOptions = Object.keys(options.franchise).filter(k => options.franchise[k]);
    
    await Promise.all(reels.map((r, i) => r.roll(getQualifyingMamano(typeOptions, franchiseOptions), i * 500 + Math.random() * 200)));
    spinning = false;
  }

  function handleOptSave(event: CustomEvent) {
    options = event.detail.options;
    optOpen = false;
  }
</script>

<div class="app">
  <div class="reels">
    {#each Array(reelCount) as _, i}
      <Reel bind:this={reels[i]} {images} />
    {/each}
  </div>
  <div class="controls">
    <button on:click={() => optOpen = true}>Options</button>
    <button on:click={() => reelCount = Math.max(1, reelCount - 1)}>-</button>
    <button on:click={() => reelCount++}>+</button>
    <button on:click={rollReels} disabled={spinning}>Spin</button>
  </div>
</div>

{#if optOpen}
  <Options {options} on:optsave={handleOptSave} />
{/if}

<style>
  .app { min-height: 100vh; display: flex; flex-direction: column; justify-content: center; /* vertical center */ align-items: center; /* horizontal center */ gap: 20px;}
  .reels { display:flex; gap:8px; justify-content:center; align-items: center; flex-wrap: wrap; gap: 16px;}
  .controls { display:flex; flex-direction: row; gap:8px; justify-content: center; align-items: center; margin-top:24px; }
</style>