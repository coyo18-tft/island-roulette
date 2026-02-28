<script lang="ts">
  import { tweened } from 'svelte/motion';
  import { quadInOut } from 'svelte/easing';
  import type { Writable } from 'svelte/store';
  import { fade, fly } from 'svelte/transition';

  export let images: { src: string; name: string }[] = [];
  export let delay = 0;

  const reelItems = 25;
  let current: number[] = [-1];
  let currentName = '';
  let spinning = false;
  const pos = tweened(0, { duration: 0, easing: quadInOut });

  export async function roll(qualify: number[], stagger = 0) {
    current = current.slice(-1);
    for (let i = 0; i < reelItems - 1; i++) current.push(qualify[Math.floor(Math.random() * qualify.length)]);
    spinning = true;
    pos.set(0, { duration: 0 });
    await new Promise(r => setTimeout(r, stagger + delay));
    await pos.set(1, { duration: 3000 });
    const index = current.at(-1);

    if (index !== undefined && index >= 0) {
      currentName = images[index].name;
    }
    spinning = false;
  }
</script>

<div class="container">
<div class="reel-window">
  <div class="reel" style="transform: translateY(-{(180 * (reelItems - 1)) * $pos}px);">
  {#each current as i}
    <div class="reel-item {i === -1 ? 'placeholder' : ''}">
      {#if i === -1}
        ?
      {:else}
        <img src={images[i].src} alt={images[i].name} />
      {/if}
    </div>
  {/each}
  </div>
</div>
  <!-- Name display -->
  {#if !spinning && currentName}
    <div class="reel-name" in:fly={{ y: 20 }} out:fade>{currentName}</div>
  {/if}
</div>

<style>
  .container { width: 150px; height: 240px; display: flex; flex-direction: column; align-items: center; position: relative;}
  .reel-window { width: 120px; height: 180px; overflow: hidden;border-radius: 12px; background: #000000; }
  .reel { display: flex; flex-direction: column;}
  .reel-item { width: 120px; height: 180px; display:flex; justify-content:center; align-items:center; font-family: var(--mono-font, monospace);
    font-weight: bold; user-select: none;}
  .reel-item img { width: 100%; height: 100%; object-fit: contain; }
  .placeholder { font-size: 5rem; line-height: 1; color: #fff;}
  .reel-name {   position: absolute; bottom: 0; left: 50%; transform: translateX(-50%); z-index: 10; padding: 2px 8px; border-radius: 6px;  background:
    rgba(0,0,0,0.65); color: white; font-family: var(--mono-font, monospace); font-size: 1rem; text-align: center; pointer-events: none;}
</style>