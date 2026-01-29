# Integrating Agentation into MrGuy SvelteKit

Quick guide to add Agentation to your MrGuy project.

## Option 1: Copy Component Directly (Recommended)

Since it's a single component with no dependencies, just copy it:

### Step 1: Copy the Component

```bash
# From agentation-svelte directory
cp src/lib/Agentation.svelte ~/mrguy-sveltekit/src/lib/components/Agentation.svelte
```

### Step 2: Add to Root Layout

Edit `~/mrguy-sveltekit/src/routes/+layout.svelte`:

```svelte
<script>
  import Agentation from '$lib/components/Agentation.svelte';
  import { dev } from '$app/environment';
  
  // ... your existing code
</script>

<!-- Your existing layout -->
<slot />

<!-- Add at the end, only in dev -->
{#if dev}
  <Agentation />
{/if}
```

### Step 3: Use It!

1. Run your dev server: `npm run dev`
2. Press `Cmd/Ctrl + Shift + A`
3. Click any element to annotate
4. Annotation copied to clipboard!

## Option 2: Install as Package (Future)

Once published to npm:

```bash
npm install agentation-svelte
```

Then import normally:

```svelte
<script>
  import { Agentation } from 'agentation-svelte';
</script>
```

## Usage with Claude Code

### Basic Workflow

1. **Identify UI issue** in your MrGuy app
2. **Press Cmd+Shift+A** to activate annotation mode
3. **Click the problematic element**
4. **Paste into Claude Code** (annotation is in clipboard)
5. **Ask Claude to fix it!**

Example annotation output:

```
Element: button
Path: body > div#app > main.container > section.hero > button.cta-button
Text: Get Started
Position: x=450, y=320
Size: 180x48
Attributes: {
  "class": "cta-button primary",
  "data-tracking": "hero-cta",
  "type": "button"
}
Timestamp: 2026-01-26T02:20:00.000Z
```

### With Programmatic Callback

For more advanced usage, capture annotations programmatically:

```svelte
<script>
  import Agentation from '$lib/components/Agentation.svelte';
  import { dev } from '$app/environment';

  function handleAnnotation(annotation) {
    // Send to your feedback API
    fetch('/api/feedback', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        type: 'ui-annotation',
        annotation,
        userAgent: navigator.userAgent,
        viewport: {
          width: window.innerWidth,
          height: window.innerHeight
        }
      })
    });

    // Still copy to clipboard
    const text = `UI Element: ${annotation.element} at ${annotation.elementPath}`;
    navigator.clipboard.writeText(text);
  }
</script>

{#if dev}
  <Agentation onAnnotationAdd={handleAnnotation} />
{/if}
```

## Real-World Examples

### Example 1: Report Button Position Issue

**You:** Claude, this button is too close to the edge

**Steps:**
1. Activate agentation (Cmd+Shift+A)
2. Click the button
3. Paste annotation into Claude Code

**Claude receives:**
```
Element: button
Position: x=1850, y=100
Size: 120x40
```

**Claude:** I can see the button is at x=1850 which is very close to the right edge. Let me add some margin...

### Example 2: Fix Text Alignment

**You:** This text looks off-center

**Steps:**
1. Annotate the element
2. Paste into Claude

**Claude receives:**
```
Element: h1
Text: Welcome to MrGuy
Position: x=500, y=200
Size: 400x60
Attributes: {
  "class": "hero-title"
}
```

**Claude:** I see the h1 has position x=500 in a container. Let me check the centering...

### Example 3: Debug z-index Issue

**You:** This dropdown is behind the header

**Steps:**
1. Annotate both elements
2. Compare positions

**Claude receives:**
```
Element 1 (dropdown):
Position: y=150
Size: 200x300

Element 2 (header):
Position: y=0
Size: 100%x80
```

**Claude:** The dropdown is positioned at y=150 which overlaps with the header at y=0-80. Let me adjust the z-index...

## Tips for Best Results

### 1. Annotate Multiple Elements

When debugging layout issues, annotate several related elements:

```
1. Click container (paste to Claude)
2. Click first child (paste to Claude)
3. Click second child (paste to Claude)
```

Claude can then see the relationship and spacing between elements.

### 2. Include Context

When pasting, add context:

```
"This button should be centered but it's not:

[paste annotation here]
"
```

### 3. Screenshot + Annotation

For complex issues:
1. Take a screenshot
2. Annotate the element
3. Give both to Claude

### 4. Track Changes

Use annotations before/after to show Claude what changed:

```
"Before fix:
[annotation]

After fix:
[annotation]

The x position changed from 450 to 500 but it's still not centered"
```

## Integration with Existing Features

### With Error Boundary

```svelte
<script>
  import Agentation from '$lib/components/Agentation.svelte';
  import { dev } from '$app/environment';

  let errorElement = null;

  function handleError(element) {
    errorElement = element;
    // Trigger annotation on error element
  }
</script>

{#if dev}
  <Agentation />
{/if}
```

### With Analytics

```svelte
<script>
  async function handleAnnotation(annotation) {
    // Track which elements users are annotating
    await fetch('/api/analytics', {
      method: 'POST',
      body: JSON.stringify({
        event: 'ui_annotation',
        element: annotation.elementPath,
        timestamp: annotation.timestamp
      })
    });
  }
</script>

<Agentation
  onAnnotationAdd={handleAnnotation}
  copyToClipboard={true}
/>
```

## Styling for MrGuy Theme

The button will automatically match your app's theme since it uses system colors. But you can customize:

```svelte
<!-- In your +layout.svelte -->
<Agentation />

<style>
  :global(.agentation-toggle) {
    /* Match MrGuy brand colors */
    background: var(--primary-color) !important;
  }
</style>
```

## Keyboard Shortcuts

- `Cmd/Ctrl + Shift + A` - Toggle annotation mode
- `ESC` - Cancel annotation
- Click - Capture element

## Troubleshooting

### Not seeing the button?

Check:
1. You're in dev mode (`npm run dev`)
2. `dev` import is working
3. No CSS conflicts with position: fixed

### Annotations not copying?

- Check browser clipboard permissions
- Try using `onAnnotationAdd` callback instead

### Wrong element selected?

- Zoom in more
- Click precisely
- Use keyboard to cancel and try again

---

**Ready to use!** Just copy the component and start annotating. 🎯
