<script>
  import { onMount } from "svelte"

  let data = []
  let searchTerm = ""
  let filteredData = []
  let displayedData = []
  let itemsPerLoad = 15
  let loading = false

  $: {
    filteredData = data.filter((item) =>
      item.name.toLowerCase().includes(searchTerm.toLowerCase())
    )
    displayedData = filteredData.slice(0, itemsPerLoad)
  }

  function loadMore() {
    if (loading) return
    loading = true
    setTimeout(() => {
      const currentLength = displayedData.length
      const moreData = filteredData.slice(
        currentLength,
        currentLength + itemsPerLoad
      )
      displayedData = [...displayedData, ...moreData]
      loading = false
    }, 500) // 模拟加载延迟
  }

  function handleScroll(e) {
    const bottom =
      e.target.scrollHeight - e.target.scrollTop <= e.target.clientHeight + 100
    if (bottom && !loading && displayedData.length < filteredData.length) {
      loadMore()
    }
  }

  onMount(async () => {
    const response = await fetch("/fonts.json")
    data = await response.json()
    window.addEventListener("scroll", handleScroll)
    return () => {
      window.removeEventListener("scroll", handleScroll)
    }
  })
</script>

<div class="container mx-auto p-4">
  <input
    type="text"
    placeholder="Search..."
    bind:value={searchTerm}
    class="input input-bordered w-full max-w-xs mb-4"
  />

  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    {#each displayedData as item}
      <div class="card bg-base-100 shadow-xl">
        <div class="card-body">
          <h2 class="card-title">{item.name}</h2>
          <p>{item.description}</p>
          <div class="card-actions justify-end">
            <a href={item.path} class="btn btn-primary">Download</a>
          </div>
        </div>
      </div>
    {/each}
  </div>

  {#if loading}
    <div class="flex justify-center mt-4">
      <div class="loading loading-spinner loading-lg"></div>
    </div>
  {/if}

  {#if displayedData.length === filteredData.length}
    <p class="text-center mt-4">No more items to load</p>
  {/if}
</div>
