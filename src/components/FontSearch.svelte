<script>
  import { onMount } from "svelte"
  import { writable } from "svelte/store"

  let query = ""
  let fonts = []
  const results = writable([])
  const selectedFont = writable(null)

  onMount(async () => {
    const response = await fetch("/fonts.json")
    fonts = await response.json()
  })

  function handleInput(event) {
    query = event.target.value.toLowerCase()
    const filtered = fonts.filter((font) =>
      font.name.toLowerCase().includes(query)
    )
    results.set(filtered)
  }

  function selectFont(font) {
    selectedFont.set(font)
    results.set([])
    query = ""
  }
</script>

<div
  class="p-4 bg-base-100 text-base-content rounded-md shadow-md max-w-lg mx-auto"
>
  <input
    type="text"
    bind:value={query}
    on:input={handleInput}
    class="input input-bordered w-full mb-2"
    placeholder="Search for CAD fonts..."
  />
  <ul
    class="menu bg-base-100 rounded-box shadow-md"
    style="display: {$results.length > 0 ? 'block' : 'none'}"
  >
    {#each $results as font}
      <li
        class="p-2 hover:bg-base-200 cursor-pointer"
        on:click={() => selectFont(font)}
      >
        {font.name}
      </li>
    {/each}
  </ul>
  {#if $selectedFont}
    <div class="mt-4 p-4 bg-base-200 rounded-box shadow-md">
      <h3 class="font-bold">{$selectedFont.name}</h3>
      <p>{$selectedFont.description}</p>
      <a href={$selectedFont.download} class="link link-primary" target="_blank"
        >Download</a
      >
    </div>
  {/if}
</div>

<style>
  ul[style*="none"] {
    display: none !important;
  }
</style>
