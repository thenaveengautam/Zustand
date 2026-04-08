# 🐻 Zustand

> A small, fast, and scalable bearbones state management solution for React.

[![npm version](https://img.shields.io/npm/v/zustand?color=orange&logo=npm)](https://www.npmjs.com/package/zustand)
[![bundle size](https://img.shields.io/bundlephobia/minzip/zustand?label=bundle%20size)](https://bundlephobia.com/package/zustand)
[![license](https://img.shields.io/github/license/pmndrs/zustand)](./LICENSE)
[![downloads](https://img.shields.io/npm/dm/zustand)](https://www.npmjs.com/package/zustand)
[![TypeScript](https://img.shields.io/badge/TypeScript-Ready-blue?logo=typescript)](https://www.typescriptlang.org/)

Zustand is a lightweight, unopinionated state management library for React. It uses simplified flux principles and has no boilerplate — no reducers, no action creators, no providers required (mostly).

---

## ✨ Features

- 🪶 **Tiny** — ~1KB minified + gzipped
- ⚡ **Fast** — Only re-renders components that consume changed state
- 🧩 **Flexible** — Works with classes, hooks, and plain JS
- 🔌 **No Providers** — No wrapping your app in context providers
- 🛠️ **Middleware Support** — Devtools, persistence, immer, and more
- 🌐 **Framework Agnostic** — Can be used outside of React too
- 💪 **TypeScript First** — Full type inference out of the box

---

## 📦 Installation

```bash
# npm
npm install zustand

# yarn
yarn add zustand

# pnpm
pnpm add zustand
```

> **Requires:** React 16.8+ and Node.js 12+

---

## 🚀 Quick Start

### 1. Create a Store

```ts
// store/useBearStore.ts
import { create } from 'zustand'

interface BearState {
  bears: number
  increase: () => void
  reset: () => void
}

const useBearStore = create<BearState>((set) => ({
  bears: 0,
  increase: () => set((state) => ({ bears: state.bears + 1 })),
  reset: () => set({ bears: 0 }),
}))

export default useBearStore
```

### 2. Use It in a Component

```tsx
// components/BearCounter.tsx
import useBearStore from '../store/useBearStore'

function BearCounter() {
  const bears = useBearStore((state) => state.bears)
  return <h1>{bears} bears around here...</h1>
}

function Controls() {
  const increase = useBearStore((state) => state.increase)
  return <button onClick={increase}>Add a bear 🐻</button>
}
```

No providers, no wrappers — it just works!

---

## 📘 Core Concepts

### Selecting State

Select only what you need to avoid unnecessary re-renders:

```ts
// ✅ Efficient — re-renders only when `bears` changes
const bears = useBearStore((state) => state.bears)

// ⚠️ Less efficient — re-renders on any state change
const store = useBearStore()
```

### Updating State

```ts
const useBoundStore = create<State>((set, get) => ({
  count: 0,

  // Partial update
  increment: () => set({ count: get().count + 1 }),

  // Functional update
  decrement: () => set((state) => ({ count: state.count - 1 })),

  // Async action
  fetchData: async () => {
    const data = await fetch('/api/data').then((r) => r.json())
    set({ count: data.count })
  },
}))
```

### Reading State Outside Components

```ts
// Get current state snapshot
const bears = useBearStore.getState().bears

// Subscribe to changes
const unsubscribe = useBearStore.subscribe(
  (state) => console.log('bears changed:', state.bears)
)

// Set state outside React
useBearStore.setState({ bears: 10 })
```

---

## 🧰 Middleware

### Devtools

Integrate with [Redux DevTools Extension](https://github.com/reduxjs/redux-devtools):

```ts
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

const useStore = create(
  devtools((set) => ({
    count: 0,
    increment: () => set((state) => ({ count: state.count + 1 })),
  }))
)
```

### Persist

Persist state to `localStorage`, `sessionStorage`, or any async storage:

```ts
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'

const useStore = create(
  persist(
    (set) => ({
      count: 0,
      increment: () => set((state) => ({ count: state.count + 1 })),
    }),
    {
      name: 'count-storage', // localStorage key
      storage: createJSONStorage(() => sessionStorage), // optional
    }
  )
)
```

### Immer

Write immutable updates with mutable syntax:

```bash
npm install immer
```

```ts
import { create } from 'zustand'
import { immer } from 'zustand/middleware/immer'

const useStore = create(
  immer((set) => ({
    todos: [] as string[],
    addTodo: (todo: string) =>
      set((state) => {
        state.todos.push(todo) // ✅ Mutate directly thanks to Immer
      }),
  }))
)
```

### Combining Middleware

```ts
const useStore = create(
  devtools(
    persist(
      immer((set) => ({
        // your state
      })),
      { name: 'my-store' }
    )
  )
)
```

---

## 🗂️ Slices Pattern

Split large stores into smaller, composable slices:

```ts
// slices/fishSlice.ts
export const createFishSlice = (set) => ({
  fish: 0,
  addFish: () => set((state) => ({ fish: state.fish + 1 })),
})

// slices/bearSlice.ts
export const createBearSlice = (set) => ({
  bears: 0,
  addBear: () => set((state) => ({ bears: state.bears + 1 })),
})

// store/useBoundStore.ts
import { create } from 'zustand'
import { createBearSlice } from './slices/bearSlice'
import { createFishSlice } from './slices/fishSlice'

const useBoundStore = create((...args) => ({
  ...createBearSlice(...args),
  ...createFishSlice(...args),
}))
```

---

## 🔄 Zustand vs. Other Libraries

| Feature              | Zustand | Redux Toolkit | Jotai  | Recoil |
|----------------------|---------|---------------|--------|--------|
| Bundle Size          | ~1KB    | ~12KB         | ~3KB   | ~21KB  |
| Boilerplate          | Minimal | Moderate      | Minimal| Minimal|
| Provider Required    | ❌ No   | ✅ Yes        | ✅ Yes | ✅ Yes |
| TypeScript Support   | ✅      | ✅            | ✅     | ✅     |
| Devtools             | ✅      | ✅            | ✅     | ✅     |
| Async Actions        | ✅      | ✅ (thunk)    | ✅     | ✅     |
| Server-Side Rendering| ✅      | ✅            | ✅     | ⚠️     |

---

## 🧪 Testing

```ts
// Reset store between tests
import useBearStore from '../store/useBearStore'

beforeEach(() => {
  useBearStore.setState({ bears: 0 })
})

test('increases bear count', () => {
  useBearStore.getState().increase()
  expect(useBearStore.getState().bears).toBe(1)
})
```

---

## 🖥️ Server-Side Rendering (SSR / Next.js)

For SSR, create a store factory to avoid shared state across requests:

```ts
import { createStore } from 'zustand'

const createBearStore = () =>
  createStore<BearState>((set) => ({
    bears: 0,
    increase: () => set((state) => ({ bears: state.bears + 1 })),
  }))
```

Use with context to scope per request. See the [SSR Guide](https://docs.pmnd.rs/zustand/guides/nextjs) for full details.

---

## 📁 Project Structure (Recommended)

```
src/
├── store/
│   ├── useBearStore.ts       # Feature-specific store
│   ├── useFishStore.ts
│   └── index.ts              # Re-export all stores
├── slices/
│   ├── bearSlice.ts
│   └── fishSlice.ts
└── components/
    └── BearCounter.tsx
```

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch: `git checkout -b feat/your-feature`
3. Commit your changes: `git commit -m "feat: add your feature"`
4. Push to the branch: `git push origin feat/your-feature`
5. Open a Pull Request

Please read [CONTRIBUTING.md](./CONTRIBUTING.md) for details on our code of conduct and development workflow.

---

## 🔗 Resources

- 📖 [Official Documentation](https://docs.pmnd.rs/zustand/getting-started/introduction)
- 🐛 [Issue Tracker](https://github.com/pmndrs/zustand/issues)
- 💬 [Discussions](https://github.com/pmndrs/zustand/discussions)
- 🗺️ [Roadmap](https://github.com/pmndrs/zustand/projects)

---

## 📄 License

MIT © [Poimandres](https://github.com/pmndrs)

---

<p align="center">
  Made with ❤️ by the <a href="https://github.com/pmndrs">Poimandres</a> open source collective
</p>
