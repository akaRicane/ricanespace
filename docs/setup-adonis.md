## Installation

- `pnpm create adonisjs@latest .`
- `git init`
- `pnpm install`
- `pnpm install -D tailwindcss postcss tailwindcss-animate autoprefixer class-variance-authority clsx tailwind-merge`
- `npx tailwindcss init`
- change file extension in .cjs and its content

```
// tailwind.config.cjs

/** @type {import('tailwindcss').Config} */
module.exports = {
  darkMode: ['class', '[data-kb-theme="dark"]'],
  content: ['./inertia/**/*.{js,ts,jsx,tsx}', './resources/views/**/*.edge'],
  prefix: '',
  theme: {
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
export const plugins = {
  tailwindcss: {},
  autoprefixer: {},
}
```

- update the vite.config.ts

```
import { getDirname } from '@adonisjs/core/helpers'
import inertia from '@adonisjs/inertia/client'
import adonisjs from '@adonisjs/vite/client'
import { defineConfig } from 'vite'
import solid from 'vite-plugin-solid'

export default defineConfig({
  plugins: [
    inertia({ ssr: { enabled: false } }),
    solid(),
    adonisjs({ entrypoints: ['inertia/app/app.tsx'], reload: ['resources/views/**/*.edge'] }),
  ],

  resolve: {
    alias: {
      '~/shadcn/': `${getDirname(import.meta.url)}/inertia/shadcn/`,
      '~/': `${getDirname(import.meta.url)}/inertia/`,
    },
  },
  assetsInclude: ['**/*.html'],
})
```

- update the tsconfig.json (root)

```
{
  "extends": "@adonisjs/tsconfig/tsconfig.app.json",
  "compilerOptions": {
    "rootDir": "./",
    "outDir": "./build",
    "paths": {
      "#config/*": ["./config/*.js"],
      "#exceptions/*": ["./app/exceptions/*.js"],
      "#middleware/*": ["./app/middleware/*.js"],
      "#shadcn/*": ["./inertia/components/ui/*"],
      "#start/*": ["./start/*.js"],
      "#tests/*": ["./tests/*.js"],
      "#types/*": ["./inertia/types/*"]
    }
  },
  "exclude": ["./inertia/**/*", "node_modules", "build"]
}
```

- and the inertia/tsconfig.json

```
{
  "extends": "@adonisjs/tsconfig/tsconfig.client.json",
  "compilerOptions": {
    "baseUrl": ".",
    "jsx": "preserve",
    "module": "ESNext",
    "jsxImportSource": "solid-js",
    "paths": {
      "~/*": ["./*"]
    }
  },
  "include": ["./**/*.ts", "./**/*.tsx", "./**/*.d.ts"]
}
```

- update components.json to properly configure shadcn

```
{
  "$schema": "https://shadcn-solid.com/schema.json",
  "tailwind": {
    "config": "tailwind.config.cjs",
    "css": {
      "path": "inertia/css/app.css",
      "variable": true
    },
    "color": "slate",
    "prefix": ""
  },
  "alias": {
    "component": "inertia/shadcn/",
    "cn": "~/shadcn/lib"
  }
}
```

- update the inertia/css/app.css

```
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;

    --card: 0 0% 100%;
    --card-foreground: 222.2 84% 4.9%;

    --popover: 0 0% 100%;
    --popover-foreground: 222.2 84% 4.9%;

    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;

    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;

    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;

    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;

    --destructive: 0 84.2% 60.2%;
    --destructive-foreground: 210 40% 98%;

    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --ring: 222.2 84% 4.9%;

    --radius: 0.5rem;
  }

  [data-kb-theme='dark'] {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;

    --card: 222.2 84% 4.9%;
    --card-foreground: 210 40% 98%;

    --popover: 222.2 84% 4.9%;
    --popover-foreground: 210 40% 98%;

    --primary: 210 40% 98%;
    --primary-foreground: 222.2 47.4% 11.2%;

    --secondary: 217.2 32.6% 17.5%;
    --secondary-foreground: 210 40% 98%;

    --muted: 217.2 32.6% 17.5%;
    --muted-foreground: 215 20.2% 65.1%;

    --accent: 217.2 32.6% 17.5%;
    --accent-foreground: 210 40% 98%;

    --destructive: 0 62.8% 30.6%;
    --destructive-foreground: 210 40% 98%;

    --border: 217.2 32.6% 17.5%;
    --input: 217.2 32.6% 17.5%;
    --ring: 212.7 26.8% 83.9%;
  }
}

@layer base {
  * {
    @apply border-border;
  }
  body {
    @apply bg-background text-foreground w-screen h-screen;
  }
}
```

- run `pnpm clean` to finalize the clean of all files in repository.

## Docker and production deployment

- add the Dockerfile

```
FROM node:20.12.2-alpine3.18 AS base

# All deps stage
FROM base AS deps
WORKDIR /app
ADD package.json ./
RUN npm install

# Production only deps stage
FROM base AS production-deps
WORKDIR /app
ADD package.json ./
RUN npm install --omit=dev

# Build stage
FROM base AS build
WORKDIR /app
COPY --from=deps /app/node_modules /app/node_modules
ADD . .
RUN npm run build

# Production stage
FROM base
ENV NODE_ENV=production
WORKDIR /app
COPY .env.production .env
COPY --from=production-deps /app/node_modules /app/node_modules
COPY --from=build /app/build /app
EXPOSE 8080
CMD ["node", "./bin/server.js"]
```

- and the .dockerignore

```
.git/
.vscode/
node_modules/
scripts/

.env
.env.example
.env.production.example
.editorconfig
pnpm-lock.yaml

make.sh
Dockerfile
```

- "local" production testing, create a make.sh

```
npm run build

mkdir -p build
mkdir -p build/temp
echo "\n\nCopying production env to build folder"
cp .env.production build/.env

echo "\n\nStarting building node modules"
cd build
npm i --omit=dev

echo "\n\nStarting app from build folder (production mode)"
node bin/server.js
```

- this bash scripts will handle the dockerization and publishing in hub.docker.com, create a containerize.sh. This one is for my website, the flag being amd as it is an amd target (linux server)

```
docker build --platform linux/amd64 -t akaricane/ricane.art:amd .
docker push akaricane/ricane.art:amd
```

## Shadcn usage

- to add a new ui-component: `pnpm dlx shadcn-solid@latest add button`
- (optional) run `pnpm format` or `pnpm clean` to clean the auto added file
