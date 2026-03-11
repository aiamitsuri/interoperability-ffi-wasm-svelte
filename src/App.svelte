<script>
  import { onMount } from 'svelte';
  import * as wasm from "@aiamitsuri/interoperability-ffi-wasm";

  let results = $state(null);
  let loading = $state(true);
  let error = $state(null);

  async function loadData() {
    loading = true;
    error = null;
    
    try {
      // Initialize Wasm (Required for Web)
      await wasm.default();

      const params = {
        language: "node",
        integration: "done",
        crates: "wasm",
        developmentkit: "app",
        page: "1",
        ids: null
      };

      const data = await wasm.fetch_from_js(params);
      console.log("Data received from Rust:", data);
      results = data;
    } catch (err) {
      console.error("Wasm Error:", err);
      error = err.toString();
    } finally {
      loading = false;
    }
  }

  onMount(() => {
    loadData();
  });
</script>

<main style="padding: 20px; font-family: sans-serif;">
  <h1>🚩 Interoperability Search (Svelte + Rust)</h1>

  <button 
    onclick={loadData} 
    disabled={loading}
    style="margin-bottom: 20px; padding: 8px 16px; cursor: pointer;"
  >
    {loading ? 'Refreshing...' : '🔄 Refresh Data'}
  </button>

  {#if loading && !results}
    <p>🚀 Initializing Rust Wasm...</p>
  {:else if error}
    <p style="color: red;">❌ Error: {error}</p>
  {:else if results}
    <div>
      <h3>Found {results.pagination?.total_items || 0} Items</h3>
      <pre style="background: #f4f4f4; padding: 10px; border-radius: 8px; overflow: auto;">
        {JSON.stringify(results, null, 2)}
      </pre>
    </div>
  {:else}
    <p>No results found.</p>
  {/if}
</main>