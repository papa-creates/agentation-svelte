# Agentation Svelte

## What This Is
Svelte component library that provides click-to-annotate UI feedback for AI coding agents. Add `<Agentation />` to your layout, press Cmd+Shift+A, hover and click any element to capture structured annotation data (tag, CSS path, bounding box, attributes) and copy to clipboard.

## Tech Stack
- **Framework:** Svelte 5 + SvelteKit
- **Build:** Vite + `svelte-package` + publint
- **Language:** TypeScript
- **Dependencies:** `runed` utility library

## Commands
```bash
npm run dev          # Dev server (demo app)
npm run build        # Vite build + svelte-package + publint
npm run package      # Build library dist only
npm run check        # svelte-check + TypeScript validation
```

## Key Files
- `src/lib/Agentation.svelte` -- the single component (entire library)
- `src/lib/index.js` -- package entry point
