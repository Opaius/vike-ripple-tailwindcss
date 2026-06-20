# @vike-ripple/tailwindcss

[Tailwind CSS v4](https://tailwindcss.com) integration for [Ripple TS](https://ripple-ts.com) — enables `@apply` inside Ripple `<style>` blocks with full theme/utility resolution.

## Install

```sh
npm install @vike-ripple/tailwindcss
```

Also requires `@tailwindcss/vite` and `@ripple-ts/vite-plugin`.

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
import vikeRipple from '@vike-ripple/vike-ripple'
import vikeRippleTailwindcss from '@vike-ripple/tailwindcss'

export default defineConfig({
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

And import it in your client renderer (`renderer/+onRenderClient.tsx`):

```tsx
import '../src/tailwind.css'
```

## Usage

Inside any `.tsrx` `<style>` block:

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

Ripple extracts CSS from `<style>` blocks and emits it as a virtual module (`file.tsrx?ripple&type=style&lang.css`). This plugin patches `@ripple-ts/vite-plugin` to prepend `@import "tailwindcss"` to the extracted CSS, so Tailwind's compiler has the full theme and utilities available when processing `@apply` directives.

The patch avoids the `Cannot apply unknown utility class` error that normally occurs when Tailwind processes CSS without the framework context.

## Known issues

- **`</style>` in JavaScript template literals**: Tailwind v4's `@tailwindcss/oxide` scanner scans all non-gitignored files. If a `.tsx` file contains `</style>` inside a JavaScript string (e.g., `const html = '<style>...</style>'`), the scanner may emit a `CssSyntaxError`. Workaround: break the literal with string concatenation: `"<" + "/style>"`. See [tailwindcss#20000](https://github.com/tailwindlabs/tailwindcss/issues/20000).
