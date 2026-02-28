<script context="module" lang="ts">
  import queryData from './assets/query.json';

  export function getDefaultOptions() {
    const opt: { type: Record<string, boolean>; franchise: Record<string, boolean> } = { type: {}, franchise: {} };
    queryData.type.sub.forEach(e => (opt.type[e.key] = true));
    queryData.franchise.sub.forEach(e => (opt.franchise[e.key] = true));
    return opt;
  }
</script>

<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  import query from './assets/query.json';

  const dispatch = createEventDispatcher();
  export let options = getDefaultOptions();

  function toggleOption(section: 'type' | 'franchise', key: string) {
    options[section][key] = !options[section][key];
    options = { ...options };
  }

  function toggleAll(section: 'type' | 'franchise', value: boolean) {
    Object.keys(options[section]).forEach(k => (options[section][k] = value));
    options = { ...options };
  }

  function saveAndClose() {
    dispatch('optsave', { options });
  }
</script>

<div class="options">
  <h3>{query.type.name}</h3>
  {#each query.type.sub as sub}
    <button class:selected={options.type[sub.key]} on:click={() => toggleOption('type', sub.key)}>{sub.name}</button>
  {/each}
  <p>{' '}</p>
  <button on:click={() => toggleAll('type', true)}>All</button>
  <button on:click={() => toggleAll('type', false)}>None</button>

  <h3>{query.franchise.name}</h3>
  {#each query.franchise.sub as sub}
    <button class:selected={options.franchise[sub.key]} on:click={() => toggleOption('franchise', sub.key)}>{sub.name}</button>
  {/each}
  <p>{' '}</p>
  <button on:click={() => toggleAll('franchise', true)}>All</button>
  <button on:click={() => toggleAll('franchise', false)}>None</button>

  <p>{' '}</p>
  <button on:click={saveAndClose}>Close</button>
</div>

<style>
  .options { padding: 16px; background: #222; color: #fff;border-radius: 16px; }
  button.selected { background: #0af; color: #fff; }
  button { margin: 4px; padding: 4px 8px; }
</style>