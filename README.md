# vike-ripple-tailwindcss

> ⚠️ **HIGHLY EXPERIMENTAL** — This package is in early development. APIs may change without notice, parts may not work, and documentation may be incomplete. Use at your own risk.

[Tailwind CSS v4](https://tailwindcss.com) integration for [Ripple TS](https://ripple-ts.com) — enables `@apply` inside Ripple `<style>` blocks with full theme/utility resolution.

## Install

```sh
npm install vike-ripple-tailwindcss
```

## Setup

### 1. Run setup

```sh
npx vike-ripple-tailwindcss setup
```

Or add to your project's `package.json`:

```json
"scripts": {
  "postinstall": "vike-ripple-tailwindcss setup"
}
```

### 2. Add plugin to `vite.config.ts`

```ts
import { defineConfig } from 'vite'
import vike from 'vike/plugin'
import { ripple } from '@ripple-ts/vite-plugin'
import tailwindcss from '@tailwindcss/vite'
import vikeRipple from 'vike-ripple'
import vikeRippleTailwindcss from 'vike-ripple-tailwindcss'

export default defineConfig({
  optimizeDeps: { exclude: ['ripple'] },
  plugins: [
    vikeRipple(),
    ripple({ excludeRippleExternalModules: true }),
    vike(),
    vikeRippleTailwindcss(),
    tailwindcss(),
  ],
})
```

### 3. Add CSS entry point

Create `src/tailwind.css`:

```css
@import "tailwindcss";
```

And import it in the page that uses Tailwind classes:

```tsx
import '../src/tailwind.css'
```

## Usage

```tsrx
<style>
  .my-button {
    @apply bg-blue-500 text-white font-bold py-2 px-4 rounded;
  }
  :global(body) {
    @apply bg-gray-50 text-gray-900;
  }
</style>
```

## How it works

Ripple extracts CSS from `<style>` blocks and emits it as a virtual module. This plugin patches `@ripple-ts/vite-plugin` to prepend `@import "tailwindcss" layer(reference)` to the extracted CSS, making Tailwind utilities available for `@apply` without generating duplicate CSS output.

## Known Issues

- **HMR hang**: Editing files with `@apply` during dev may occasionally cause HMR to hang. Restarting the dev server resolves it.
- **`</style>` in template literals**: If a file contains `</style>` inside a JavaScript string, the Tailwind Oxide scanner may emit a `CssSyntaxError`. Workaround: break the literal with string concatenation: `"<" + "/style>"`. See [tailwindcss#20000](https://github.com/tailwindlabs/tailwindcss/issues/20000).
