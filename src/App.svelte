<script>
  import * as wasm from "@aiamitsuri/interoperability-ffi-wasm";

  let results = $state(null);
  let loading = $state(true);
  let page = $state(1);

  async function loadData(p) {
    loading = true;
    try {
      await wasm.default();
      results = await wasm.fetch_from_js({ 
        page: p.toString(),
        ids: null 
      });
    } catch (err) {
      console.error("WASM Error:", err);
    } finally {
      loading = false;
    }
  }

  $effect(() => {
    loadData(page);
  });

  const handleNext = () => page++;
  const handlePrev = () => { if (page > 1) page--; };
</script>

<div style="padding: 20px; font-family: sans-serif; text-align: left; max-width: 1000px;">
  <h1 style="margin-top: 0;">Interoperability FFI (Svelte)</h1>

  {#if loading && !results}
    <p>🚀 Initializing Rust Wasm...</p>
  {:else}
    <div style="margin-bottom: 20px; display: flex; gap: 12px; align-items: center;">
      <button onclick={handlePrev} disabled={loading || !results?.pagination?.prev_page_url}>
        Previous
      </button>

      <span style="font-weight: bold;">
        Page {results?.pagination?.current_page || page} / {results?.pagination?.total_pages || 1}
      </span>

      <button onclick={handleNext} disabled={loading || !results?.pagination?.next_page_url}>
        Next
      </button>
    </div>

    {#if results?.data}
      <div style="display: flex; flex-direction: column; gap: 15px;">
        {#each results.data as item}
          <div style="background: #f9f9f9; border: 1px solid #eee; padding: 15px; border-radius: 8px;">
            <h3 style="margin: 0 0 10px 0;">{item.title}</h3>
            
            <div style="display: flex; gap: 15px; font-size: 0.9em; color: #666; margin-bottom: 8px; flex-wrap: wrap;">
              <span><strong>Language:</strong> {item.language}</span>
              <span style="color: {item.integration === 'completed' ? 'green' : 'orange'};">
                <strong>Integration:</strong> {item.integration}
              </span>
              
              <!-- Only show Opensources if NOT pending -->
              {#if item.integration !== 'pending' && item.opensources}
                <span style="color: #007bff; font-weight: bold;">
                  📂 Opensources: {item.opensources.length}
                </span>
              {/if}
            </div>

            <div style="font-size: 0.85em; display: flex; flex-direction: column; gap: 4px;">
              {#if item.crates}
                <p style="margin: 0;"><strong>Crates:</strong> {item.crates.join(', ')}</p>
              {/if}

              {#if item.developmentkit}
                <p style="margin: 0;"><strong>Development Kit:</strong> {item.developmentkit.join(', ')}</p>
              {/if}
            </div>
          </div>
        {/each}
      </div>
    {:else}
      <p>No results found.</p>
    {/if}
  {/if}
</div>

<style>
  button {
    padding: 6px 12px;
    cursor: pointer;
  }
  button:disabled {
    cursor: not-allowed;
    opacity: 0.6;
  }
</style>