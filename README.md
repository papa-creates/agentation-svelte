# Agentation for Svelte

**Visual feedback for AI coding agents** - Svelte port of [Agentation](https://agentation.dev)

Annotate UI elements with a single click and get structured data for your AI agent. Perfect for use with Claude Code.

## ✨ Features

- 🎯 Click-to-annotate any UI element
- 📋 Auto-copy to clipboard
- 🎨 Visual highlight overlay
- ⌨️ Keyboard shortcut (Cmd/Ctrl + Shift + A)
- 📊 Structured annotation data
- 🔌 Programmatic callbacks
- 🚫 Zero dependencies (beyond Svelte)
- 🖥️ Desktop-optimized

## 🚀 Quick Start

### Install

```bash
npm install agentation-svelte
# or
pnpm add agentation-svelte
# or
yarn add agentation-svelte
```

### Basic Usage

Add to your root layout (`+layout.svelte`):

```svelte
<script>
  import { Agentation } from 'agentation-svelte';
  import { dev } from '$app/environment';
</script>

<slot />

{#if dev}
  <Agentation />
{/if}
```

That's it! Press **Cmd/Ctrl + Shift + A** to start annotating.

## 📖 Usage

### Default (Clipboard Mode)

```svelte
<script>
  import { Agentation } from 'agentation-svelte';
</script>

<!-- Automatically copies annotations to clipboard -->
<Agentation />
```

### Programmatic Mode

```svelte
<script>
  import { Agentation } from 'agentation-svelte';

  function handleAnnotation(annotation) {
    console.log('Element:', annotation.element);
    console.log('Path:', annotation.elementPath);
    console.log('Text:', annotation.text);
    console.log('Position:', annotation.boundingBox);
    
    // Send to your agent/API
    sendToAgent(annotation);
  }
</script>

<Agentation
  onAnnotationAdd={handleAnnotation}
  copyToClipboard={false}
/>
```

### Disable in Production

```svelte
<script>
  import { Agentation } from 'agentation-svelte';
  import { dev } from '$app/environment';
</script>

<!-- Only shows in development -->
{#if dev}
  <Agentation />
{/if}
```

## 🎛️ Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `onAnnotationAdd` | `(annotation: Annotation) => void` | `undefined` | Callback when annotation is added |
| `copyToClipboard` | `boolean` | `true` | Auto-copy to clipboard |
| `enabled` | `boolean` | `true` | Enable/disable component |

## 📊 Annotation Data Structure

```typescript
interface Annotation {
  element: string;           // "button", "div", etc.
  elementPath: string;       // "body > main > div.container > button#submit"
  text: string;              // Element text content
  boundingBox: {
    x: number;
    y: number;
    width: number;
    height: number;
  };
  attributes: {              // All element attributes
    [key: string]: string;
  };
  timestamp: string;         // ISO 8601 timestamp
}
```

## 🎯 How to Use

1. **Activate:** Press `Cmd/Ctrl + Shift + A` or click the blue button
2. **Hover:** Move mouse over elements - they highlight in blue
3. **Click:** Click element to capture annotation
4. **Use:** Annotation is copied to clipboard (or sent to callback)

## 💡 Use Cases

### With Claude Code

```svelte
<script>
  import { Agentation } from 'agentation-svelte';

  function handleAnnotation(annotation) {
    // Format for Claude Code
    const feedback = `
Please update this element:
- Element: ${annotation.element}
- Location: ${annotation.elementPath}
- Current text: "${annotation.text}"
- Position: x=${annotation.boundingBox.x}, y=${annotation.boundingBox.y}
`;
    
    // Copy to clipboard for Claude
    navigator.clipboard.writeText(feedback);
  }
</script>

<Agentation onAnnotationAdd={handleAnnotation} />
```

### Send to API

```svelte
<script>
  async function handleAnnotation(annotation) {
    await fetch('/api/feedback', {
      method: 'POST',
      body: JSON.stringify(annotation)
    });
  }
</script>

<Agentation
  onAnnotationAdd={handleAnnotation}
  copyToClipboard={false}
/>
```

### Debug Mode

```svelte
<script>
  import { dev } from '$app/environment';
  
  function handleAnnotation(annotation) {
    console.table(annotation);
    console.log('Bounding box:', annotation.boundingBox);
    console.log('Attributes:', annotation.attributes);
  }
</script>

{#if dev}
  <Agentation onAnnotationAdd={handleAnnotation} />
{/if}
```

## 🎨 Customization

The component uses these colors by default:
- Active button: Blue (#3b82f6)
- Cancel button: Red (#ef4444)
- Highlight: Blue with transparency
- Count badge: Green (#10b981)

Styles are scoped to the component but can be overridden.

## ⌨️ Keyboard Shortcuts

- `Cmd/Ctrl + Shift + A` - Toggle annotation mode
- `ESC` - Exit annotation mode
- Click element - Capture annotation

## 🔒 Security & Privacy

- **100% client-side** - No data sent anywhere
- **No network requests** - All processing is local
- **Dev-only** - Use environment checks to exclude from production
- **No tracking** - Nothing is stored or transmitted

## 📦 SvelteKit Integration

### In `+layout.svelte`

```svelte
<script>
  import { Agentation } from 'agentation-svelte';
  import { dev } from '$app/environment';
</script>

<slot />

{#if dev}
  <Agentation />
{/if}
```

### In Individual Pages

```svelte
<!-- routes/+page.svelte -->
<script>
  import { Agentation } from 'agentation-svelte';
</script>

<h1>My Page</h1>

<!-- Only on this page -->
<Agentation />
```

### With Conditional Loading

```svelte
<script>
  import { browser, dev } from '$app/environment';
  import { onMount } from 'svelte';

  let showAgentation = false;

  onMount(() => {
    // Only load if Cmd+Shift+D is pressed
    const handler = (e) => {
      if (e.metaKey && e.shiftKey && e.key === 'D') {
        showAgentation = true;
      }
    };
    window.addEventListener('keydown', handler);
    return () => window.removeEventListener('keydown', handler);
  });
</script>

{#if browser && dev && showAgentation}
  <Agentation />
{/if}
```

## 🆚 Comparison to React Version

| Feature | React | Svelte |
|---------|-------|--------|
| Bundle size | ~8KB | ~6KB |
| Dependencies | React 18+ | Svelte 4/5 |
| API | Same | Same |
| Performance | Fast | Faster |
| Setup | `<Agentation />` | `<Agentation />` |

## 🐛 Troubleshooting

### Component doesn't show

- Check `enabled` prop is `true`
- Ensure running in browser (not SSR)
- Check `dev` environment if using conditional

### Keyboard shortcut doesn't work

- Make sure focus is on the page (click somewhere first)
- Try clicking the blue button instead
- Check browser console for errors

### Annotations not copying

- Check `copyToClipboard` is `true`
- Verify clipboard permissions in browser
- Use `onAnnotationAdd` callback as alternative

## 📝 Examples

See `examples/` directory for:
- Basic SvelteKit integration
- Programmatic usage
- API integration
- Custom styling

## 🤝 Contributing

This is a Svelte port of the original React version. Issues and PRs welcome!

## 📄 License

MIT

## 🙏 Credits

- Original [Agentation](https://agentation.dev) by [Benji Taylor](https://x.com/benjitaylor), [Dennis Jin](https://x.com/seldom), and [Alex Vanderzon](https://x.com/alexvanderzon)
- Svelte port by [Abdias @ Loving Art & Company](https://lovingartandcompany.com)

---

**Made for Claude Code and SvelteKit** 🚀
