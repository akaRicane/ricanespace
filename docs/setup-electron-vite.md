# Electron-Vite installation

- `git init`
- `git branch -m main`
- `pnpm install electron-vite`
- `pnpm create @quick-start/electron .`
  - solid
  - typescript
  - no auto update
  - no electron proxy mirror
- `pnpm install`
- `pnpm dev`

<br/>

# Extra dependencies

## Tailwind

- `pnpm install -D tailwindcss postcss tailwindcss-animate autoprefixer`
- `npx tailwindcss init`
- change file extension in .cjs and its content

```
/** @type {import('tailwindcss').Config} */
module.exports = {
  darkMode: ['class', '[data-kb-theme="dark"]'],
  content: ['src/renderer/src/**/*.{html,js,jsx,ts,tsx}'],
  prefix: '',
  theme: {
    container: {
      center: true,
      padding: '2rem',
      screens: {
        '2xl': '1400px'
      }
    },
    extend: {
      colors: {
        border: 'hsl(var(--border))',
        input: 'hsl(var(--input))',
        ring: 'hsl(var(--ring))',
        background: 'hsl(var(--background))',
        foreground: 'hsl(var(--foreground))',
        primary: {
          DEFAULT: 'hsl(var(--primary))',
          foreground: 'hsl(var(--primary-foreground))'
        },
        secondary: {
          DEFAULT: 'hsl(var(--secondary))',
          foreground: 'hsl(var(--secondary-foreground))'
        },
        destructive: {
          DEFAULT: 'hsl(var(--destructive))',
          foreground: 'hsl(var(--destructive-foreground))'
        },
        muted: {
          DEFAULT: 'hsl(var(--muted))',
          foreground: 'hsl(var(--muted-foreground))'
        },
        accent: {
          DEFAULT: 'hsl(var(--accent))',
          foreground: 'hsl(var(--accent-foreground))'
        },
        popover: {
          DEFAULT: 'hsl(var(--popover))',
          foreground: 'hsl(var(--popover-foreground))'
        },
        card: {
          DEFAULT: 'hsl(var(--card))',
          foreground: 'hsl(var(--card-foreground))'
        }
      },
      borderRadius: {
        lg: 'var(--radius)',
        md: 'calc(var(--radius) - 2px)',
        sm: 'calc(var(--radius) - 4px)'
      },
      keyframes: {
        'accordion-down': {
          from: { height: 0 },
          to: { height: 'var(--kb-accordion-content-height)' }
        },
        'accordion-up': {
          from: { height: 'var(--kb-accordion-content-height)' },
          to: { height: 0 }
        },
        'collapsible-down': {
          from: { height: 0 },
          to: { height: 'var(--kb-collapsible-content-height)' }
        },
        'collapsible-up': {
          from: { height: 'var(--kb-collapsible-content-height)' },
          to: { height: 0 }
        },
        'caret-blink': {
          '0%,70%,100%': { opacity: '1' },
          '20%,50%': { opacity: '0' }
        }
      },
      animation: {
        'accordion-down': 'accordion-down 0.2s ease-out',
        'accordion-up': 'accordion-up 0.2s ease-out',
        'collapsible-down': 'collapsible-down 0.2s ease-out',
        'collapsible-up': 'collapsible-up 0.2s ease-out',
        'caret-blink': 'caret-blink 1.25s ease-out infinite'
      }
    }
  },
  plugins: [require('tailwindcss-animate')]
}
```

- add the postcss.config.js file

```
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {}
  }
}
```

## Shadcn Ui

- `pnpm dlx shadcn-solid@latest init`
  - Tailwindcss
  - Slate
  - src/renderer/src/assets/main.css
  - Yes
  - (leave blank)
  - leave as is (will be modified afterwards)
- update tsconfig.web.json compilerOptions definition

```
"compilerOptions": {
    "composite": true,
    "jsxImportSource": "solid-js",
    "baseUrl": ".",
    "paths": {
      "@renderer/*": [
        "src/renderer/src/*"
      ],
      "@shadcn/*": [
        "src/renderer/src/components/shadcn/*"
      ],
      "@types": [
        "src/types/*"
      ]
    }
  }
```

- update electron.vite.config.ts to edit properly aliases (also add the alias to define common types under src/types/types.ts)

```
import { defineConfig, externalizeDepsPlugin } from 'electron-vite'
import { resolve } from 'path'
import solid from 'vite-plugin-solid'

export default defineConfig({
  main: {
    plugins: [externalizeDepsPlugin()]
  },
  preload: {
    plugins: [externalizeDepsPlugin()]
  },
  renderer: {
    resolve: {
      alias: {
        '@renderer': resolve('src/renderer/src'),
        '@shadcn': resolve('src/renderer/src/components/shadcn'),
        '@types': resolve('src/types')
      }
    },
    plugins: [solid()]
  }
})

```

- update components.json

```
{
  "$schema": "https://shadcn-solid.com/schema.json",
  "tailwind": {
    "config": "tailwind.config.cjs",
    "css": {
      "path": "src/renderer/src/assets/main.css",
      "variable": true
    },
    "color": "slate",
    "prefix": ""
  },
  "alias": {
    "component": "src/renderer/src/components/shadcn",
    "cn": "@shadcn/lib"
  }
}

```

- a "@" folder will be created by default. Move the file under a new folder in "src/renderer/src/components/shadcn" and rename the file lib.ts. You can create a ui folder too under shadcn one. Delete @ folder.
- test `pnpm dlx shadcn-solid@latest add button`
  - if the button.tsx is not created under shadcn/ui folder, make sure you have the proper aliases defined in components.json
- edit renderer/src/App.tsx to test and validate the shadcn ui button correct implementation

```
import { Button } from '@shadcn/ui/button'
import type { Component } from 'solid-js'

const App: Component = () => {
  const ipcHandle = (): void => window.electron.ipcRenderer.send('ping')

  return (
    <>
      <div class="action">
        <Button onClick={ipcHandle}>Send IPC check</Button>
      </div>
    </>
  )
}

export default App
```

- to stay type checked in TS, need to add to shadcn components the return type: JSXElement

## Finish installation

- remove everything except main.css in renderer/assets folder
