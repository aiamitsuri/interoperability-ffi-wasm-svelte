<script>
  import * as wasm from "@aiamitsuri/interoperability-ffi-wasm";

  let results = $state(null);
  let loading = $state(true);
  let error = $state(null);
  let page = $state(1);

  async function loadData(currentPage) {
    loading = true;
    try {
      await wasm.default();
      const params = {
        language: null,
        integration: null,
        crates: null,
        developmentkit: null,
        page: currentPage.toString(),
        ids: null,
      };
      const data = await wasm.fetch_from_js(params);
      results = data;
    } catch (err) {
      error = err.toString();
    } finally {
      loading = false;
    }
  }

  $effect(() => {
    const p = page;
    loadData(p);
  });

  const handleNext = () => page++;
  const handlePrev = () => { if (page > 1) page--; };
</script>

<div style="padding: 20px; font-family: sans-serif; text-align: left; max-width: 1000px;">
  <h1 style="margin-top: 0;">Interoperability Search</h1>

  {#if loading && !results}
    <p>🚀 Initializing Rust Wasm...</p>
  {:else if error}
    <p style="color: red;">❌ Error: {error}</p>
  {:else}
    <div style="margin-bottom: 20px; display: flex; gap: 12px; align-items: center; justify-content: flex-start;">
      <button onclick={() => loadData(page)} disabled={loading} style="padding: 6px 12px; cursor: pointer;">
        {loading ? 'Refreshing...' : '🔄 Refresh'}
      </button>

      <button 
        onclick={handlePrev} 
        disabled={loading || !results?.pagination?.prev_page_url}
        style="padding: 6px 12px; cursor: pointer;"
      >
        Previous
      </button>

      <span style="font-weight: bold;">
        Page {results?.pagination?.current_page || page} / {results?.pagination?.total_pages || 1}
      </span>

      <button 
        onclick={handleNext} 
        disabled={loading || !results?.pagination?.next_page_url}
        style="padding: 6px 12px; cursor: pointer;"
      >
        Next
      </button>
    </div>

    {#if results}
      <div>
        <h3 style="margin-bottom: 8px;">Found {results.pagination?.total_items || 0} Items</h3>
        <pre style="background: #f4f4f4; padding: 15px; border-radius: 8px; overflow-x: auto; white-space: pre-wrap; word-wrap: break-word;">{JSON.stringify(results, null, 2)}</pre>
      </div>
    {:else}
      <p>No results found.</p>
    {/if}
  {/if}
</div>

<style>
  button:disabled {
    cursor: not-allowed;
    opacity: 0.6;
  }
</style>