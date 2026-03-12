# interoperability-ffi-wasm-svelte

# Initialize Svelte project
- npm create vite@latest interoperability-ffi-wasm-svelte -- --template svelte
- cd interoperability-ffi-wasm-svelte
- npm install

# Install package and Wasm plugins
- npm install @aiamitsuri/interoperability-ffi-wasm@0.1.12
- npm install -D vite-plugin-wasm vite-plugin-top-level-await

# Run
- npm run dev
